# go-interface
showcasing flaw of interfaces in Go

```
package main

type myInterface interface {
	Method()
}

type myStructure struct {
}

func (myStructure) Method() {

}

func (myStructure) calculate(variable string, var2 int, var3 any) {

}

func consumeReg(s myStructure) {

}

func consumeP(s *myStructure) {

}

func consumeI(i myInterface) {

}

func createRegular() myStructure {
	s := myStructure{}
	return s
}

func createP() *myStructure {
	s := myStructure{}
	return &s
}

func main() {
	s1 := createRegular()
	consumeI(s1)

	s2 := createP()
	consumeI(s2)
}

```

```
package main

import (
	"fmt"
)

type BigDoNotCopy1 interface { // this one is an interface, interchangeable with the struct below
	Print()
}

type BigDoNotCopy2 struct { // this one is a struct, interchangeable with the interface above
	content string
}

func (BigDoNotCopy2) Print() {}

func consume1(howToPassThis BigDoNotCopy1) { // ALWAYS GOOD, because this is an INTERFACE
	fmt.Printf("interface: %p\n", howToPassThis)
	fmt.Printf("address interface: %p\n", &howToPassThis)
}

func consume2(howToPassThis BigDoNotCopy2) { // SAME signature as above, CAN BE A MISTAKE because this a STRUCT
	fmt.Printf("copy: %p\n", &howToPassThis)
}

func consume3(howToPassThis *BigDoNotCopy2) { // good, enforced
	fmt.Printf("reference: %p\n", howToPassThis)
}

func consume4(howToPass *BigDoNotCopy1) { // wrong
	fmt.Printf("reference: %p\n", howToPass)
}

func obfuscatedConstructor1() BigDoNotCopy1 {
	return &BigDoNotCopy2{}
}

func obfuscatedConstructor2() BigDoNotCopy1 {
	return BigDoNotCopy2{}
}

func main() {
	big := BigDoNotCopy2{}
	fmt.Printf("address: %p\n", &big)
	consume1(&big) // good
	consume1(big)  // valid but mistake, the calling location is making the mistake
	consume2(big)  // bad

	consume3(&big) // good but need to use pointer semtantic

	big2 := &BigDoNotCopy2{} // need to remember this for later in the code
	consume3(big2)           // good but you had to keep track of constructor

	what := obfuscatedConstructor1()
	fmt.Printf("reference what: %p\n", what)
	consume1(what) // good

	what2 := obfuscatedConstructor2()
	fmt.Printf("copy what: %p\n", &what2)
	consume1(what2)

	consume4(big2) // doesn't work
}
```
