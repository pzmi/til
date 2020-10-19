# Nesting and embedding structs

```golang
package main

import (
	"encoding/json"
	"fmt"
)

type address struct {
	Street string `json:"street"`
	City   string `json:"city"`
}

type person struct {
	Name    string
	Address address
}

type personE struct {
	Name string
	address
}

func main() {
	p := &person{
		Name: "Jan",
		Address: address{
			Street: "Rynek",
			City:   "Cracow",
		},
	}

	pe := &personE{
		Name: "Jan",
		address: address{
			Street: "Rynek",
			City:   "Cracow",
		},
	}
	fmt.Printf("struct nested: %+v\n", p)
	fmt.Printf("struct embedded: %+v\n", pe)

	s, err := json.Marshal(p)
	if err != nil {
		fmt.Println(err)
	}

	fmt.Printf("json nested: %v\n", string(s))

	se, err := json.Marshal(pe)
	if err != nil {
		fmt.Println(err)
	}
	fmt.Printf("json embedded: %v\n", string(se))
}
````
