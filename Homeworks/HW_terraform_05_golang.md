# Домашняя работа к к занятию "7.5. Основы golang"

## Задание 1
Установить golang
```
maksim@MacBook-Pro demo % go version
go version go1.18.3 darwin/amd64
```

## Задание 2


## Задание 3.1
> Напишите программу для перевода метров в футы (1 фут = 0.3048 метр). Можно запросить исходные данные у пользователя, а можно статически задать в коде. Для взаимодействия с пользователем можно использовать функцию `Scanf`.
```
package main

import "fmt"

func main() {
    fmt.Print("Enter the value in meters: ")
    var input float64
    fmt.Scanf("%f", &input)

    output := input / 0.3048

    fmt.Println("Feets:", output)
}
```
```
maksim@MacBook-Pro devops-netology % go run task3.go
Enter the value in meters: 100
Feets: 328.0839895013123
```

## Задание 3.2
> Напишите программу, которая найдет наименьший элемент в любом заданном списке, например:
> x := []int{48,96,86,68,57,82,63,70,37,34,83,27,19,97,9,17,}
```
package main

import "fmt"

func main() {
    x := []int{48, 96, 86, 68, 57, 82, 63, 70, 37, 34, 83, 27, 19, 97, 9, 17, -1}
	cur := 0
	for i, l := range x {
		if i == 0 {
			cur = l
		} else {
			if l < cur {
				cur = l
			}
		}

	}
	fmt.Println("Min element:", cur)
}
```
```
maksim@MacBook-Pro devops-netology % go run task3.2.go
Min element: -1
```

## Задание 3.3
> Напишите программу, которая выводит числа от 1 до 100, которые делятся на 3. То есть (3, 6, 9, …).
```
package main

import "fmt"

func main() {
	for i := 1; i <= 100; i++ {
		if (i % 3) == 0 {
			fmt.Print(i, " ")
		}
	}
}
```
```
maksim@MacBook-Pro devops-netology % go run task3.3.go
3 6 9 12 15 18 21 24 27 30 33 36 39 42 45 48 51 54 57 60 63 66 69 72 75 78 81 84 87 90 93 96 99
```

