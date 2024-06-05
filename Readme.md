package main

import (
	"bufio"
	"fmt"
	"os"
)

func alternate(s string) int32 {
	max := int32(0)
	
	for c1 := 'a'; c1 <= 'z'; c1++ {
		for c2 := c1 + 1; c2 <= 'z'; c2++ {
			length := int32(0)
			prev := rune(0)
			valid := true

			for _, next := range s {
				if next == c1 || next == c2 {
					if next != prev {
						length++
						prev = next
					} else {
						valid = false
						break
					}
				}
			}

			if valid && length > max {
				max = length
			}
		}
	}
	
	if max <= 1 {
		return 0
	}
	return max
}

func main() {
	reader := bufio.NewReader(os.Stdin)
	var n int
	fmt.Scanf("%d\n", &n)
	s, _ := reader.ReadString('\n')
	s = s[:len(s)-1] // remove the newline character
	fmt.Println(alternate(s))
}
