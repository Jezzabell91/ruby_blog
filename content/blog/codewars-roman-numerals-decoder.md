---
path: roman-numerals-decoder
date: 2020-12-03T22:33:39.701Z
title: CodeWars - Roman Numerals Decoder
description: I post my embarrassing solution and investigate some of the better ones
---
I am starting this series because of a recent challenge I completed on CodeWars that left me feeling once again like chonky Pikachu.

![My solution vs CodeWars Solutions](https://pbs.twimg.com/media/EhnaGWuU8AYrPPO?format=png&name=small)

My solution was unwieldy and ugly and ultimately not something I was proud of. But the top solutions were not ugly, instead they demonstrated the elegance and grace of good Ruby code. 




## The Challenge

```plain
Create a function that takes a Roman numeral as its argument and returns its value as a numeric decimal integer. You don't need to validate the form of the Roman numeral.

Modern Roman numerals are written by expressing each decimal digit of the number to be encoded separately, starting with the leftmost digit and skipping any 0s. So 1990 is rendered "MCMXC" (1000 = M, 900 = CM, 90 = XC) and 2008 is rendered "MMVIII" (2000 = MM, 8 = VIII). The Roman numeral for 1666, "MDCLXVI", uses each letter in descending order.

Example:

solution('XXI') # should return 21
Help:

Symbol    Value
I          1
V          5
X          10
L          50
C          100
D          500
M          1,000
```

## My Solution
<details>
    <summary>Click to see it... it's not pretty</summary>

![I'm not good code, I'm a monster](https://i.imgur.com/YuzjcbU.png)


```ruby
def solution(roman)
    result = 0 

    rn_array = roman.split('')
    remaining_chars_length = rn_array.length
    while remaining_chars_length > 0
        char = rn_array[rn_array.length - remaining_chars_length]

        if rn_array[rn_array.length - remaining_chars_length + 1].nil? == false 
            next_char = rn_array[rn_array.length - remaining_chars_length + 1]
        else
            next_char = ""
        end

        if char == "I"
            if next_char == "V"
                remaining_chars_length -= 2 
                result += 4
            elsif next_char == "X"
                remaining_chars_length -= 2 
                result += 9
            else
                remaining_chars_length -= 1
                result += 1
            end
        elsif char == "V"
                remaining_chars_length -= 1
                result += 5
        elsif char == "X"
            if next_char == "L"
                remaining_chars_length -= 2 
                result += 40
            elsif next_char == "C"
                remaining_chars_length -= 2 
                result += 90
            else
                remaining_chars_length -= 1
                result += 10
            end
        elsif char == "L"
                remaining_chars_length -= 1
                result += 50
        elsif char == "C"
            if next_char == "D"
                remaining_chars_length -= 2 
                result += 400
            elsif next_char == "M"
                remaining_chars_length -= 2 
                result += 900
            else
                remaining_chars_length -= 1
                result += 100
            end
        elsif char == "D"
            remaining_chars_length -= 1
            result += 500
        elsif char == "M"
            remaining_chars_length -= 1
            result += 1000
        end
    end

    return result
end
```


</details>

## Other Submitted Solutions

_Here are some of the nicer solutions to the challenge:_

### fullc0ntr0l's Solution

```ruby
def fullc0ntr0l_solution(roman)
    vals = {"M" => 1000,
            "D" => 500,
            "C" => 100,
            "L" => 50,
            "X" => 10,
            "V" => 5,
            "I" => 1
        }

    roman.chars.map{|c| vals[c]}.each_cons(2).map { |a, b| a < b ? -a : a }.concat([vals[roman[-1]]]).inject(:+)
end
```

fullc0ntr0l's solution uses two methods that I am not familiar with, #each_cons and #inject. It turns out that [#inject](https://ruby-doc.org/core-2.7.2/Enumerable.html#method-i-inject) is an alias for #reduce. 

 [#each_cons](https://ruby-doc.org/core-2.7.2/Enumerable.html#method-i-each_cons) iterates the given block for each array of consecutive (n) elements. 

 Breaking down fullc0ntr0l's solution for "CXLVIII":
 ```ruby
roman.chars
#=> ["C", "X", "L", "V", "I", "I", "I"]
 ```
The String class method [#chars](https://ruby-doc.org/core-2.7.2/String.html#method-i-chars) returns an array with each character of the string as an element. 

 ```ruby
roman.chars.map{|c| vals[c]}
#=> [100, 10, 50, 5, 1, 1, 1]
 ```
Mapping over the array replaces the characters with the corresponding values in the vals hash. 

 ```ruby
roman.chars.map{|c| vals[c]}.each_cons(2)
#=>#<Enumerator: [100, 10, 50, 5, 1, 1, 1]:each_cons(2)>
 ```
If no block is passed to #each_cons(n) then it returns the Enumerator object.

```ruby
roman.chars.map{|c| vals[c]}.each_cons(2) { |a| print a}
#=>[100, 10][10, 50][50, 5][5, 1][1, 1][1, 1]nil
```
I've done this to get an idea of what #each_cons is doing. For each element it's returning an array with the next element. The last element of the array does not have a succeeding element and returns nil.   

```ruby
roman.chars.map{|c| vals[c]}.each_cons(2).map { |a, b| a < b ? -a : a }
#=> [100, -10, 50, 5, 1, 1]
```
The map block compares each pair of values and if the first value is less than the second value it is returned as a negative. This is the part of the solution that handles cases like "IV" and "XL". 


```ruby
roman.chars.map{|c| vals[c]}.each_cons(2).map { |a, b| a < b ? -a : a }.concat([vals[roman[-1]]])
#=>[100, -10, 50, 5, 1, 1, 1]
```
#concat is used to add back the last element of the array that was cut off when #each_cons was called. The + operator could have been used in place of #concat. 

```ruby
roman.chars.map{|c| vals[c]}.each_cons(2).map { |a, b| a < b ? -a : a }.concat([vals[roman[-1]]]).inject(:+)
#=> 148
```
Finally #inject is used with the shorthand (:+) to sum the values. In this instance the Array class method #sum works as well. 

It is a good looking solution but, as with many solutions, I find it hard to look at it and quickly understand what is happening. There was however one solution that got my attention and I think it is the best in terms of simplicity, outside the box thinking and performance. 

### Fantomius' Solution

A user by the name of _Fantomius_ submitted this solution:

```ruby
def fantomius_solution(roman)
    acc = 0
    acc += roman.count('I')
    acc += roman.count('V') * 5
    acc += roman.count('X') * 10
    acc += roman.count('L') * 50
    acc += roman.count('C') * 100
    acc += roman.count('D') * 500
    acc += roman.count('M') * 1000
    acc -= roman.scan('IV').size() * 2
    acc -= roman.scan('IX').size() * 2
    acc -= roman.scan('XL').size() * 20
    acc -= roman.scan('XC').size() * 20
    acc -= roman.scan('CD').size() * 200
    acc -= roman.scan('CM').size() * 200
    return acc
end
```

_Fantomius_ managed to turn it into a series of addition and subtraction. No need for a hash, no chaining of complicated methods, just simple math. The combination of #scan and #size returns the number of instances of the pattern. The * 2 is used because of double counting, e.g. without the subtraction IX returns 11, with the subtraction IX returns 9, the correct value. 

## Summary

While I am not happy with my own solution. I have found it beneficial to take a deep look at other solutions. The process of breaking them down will hopefully increase my own ability.  Stay tuned to see if I get better. 