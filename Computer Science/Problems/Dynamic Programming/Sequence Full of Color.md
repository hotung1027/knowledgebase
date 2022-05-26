---
title : Sequence Full of Color
tags : 
- Hacker-Rank
---
###### tags: `Hacker Rank`
# Sequence Full of Color

# Problem


Source
---
[Sequence Full of Color](https://www.hackerrank.com/challenges/sequence-full-of-colors/)

Problem Analytica
---

### The original problem
You are given a sequence of  balls in 4 colors: red, green, yellow and blue. The sequence is full of colors if and only if all of the following conditions are true:

- There are as many red balls as green balls.
- There are as many yellow balls as blue balls.
- Difference between the number of red balls and green balls in every prefix of the sequence is at most 1.
- Difference between the number of yellow balls and blue balls in every prefix of the sequence is at most 1.

Your task is to write a program, which for a given sequence prints True if it is full of colors, otherwise it prints False.

### Analytica
> To solve the problem, we first have to understand the problem.

Let's define the notation as below:
- Red Balls are denote as `R`
- Green Balls are denote as `G`
- Blue Balls are denote as `B`
- Yellow Balls are denote as `Y`

and the rules can we rephrase as this,
- 'R' and 'G' and a pair
- 'B' and 'Y' and a pair
- at any point in linear scanning, the difference of any color in a pair must $\leq$ 1
    i.e. |count(`R`) - count(`G`)| $\leq$ 1 and |count(`B`) - count(`Y`)| $\leq$ 1
- count(`R`) == count(`G`)  and count(`B`) == count(`Y`)



Point of Attack
---
After we understand the problem, there is two point to attack this question.
1. monitor the difference in color in the scanning
2. check the pairs are balanced


Algorithm
---
````haskell
isBalanceColor :: (Ord a1, Ord a2, Num a1, Num a2) => (a1, a1, a2, a2) -> Bool
isBalanceColor (r, g, b, y) = abs (r-g) <= 1 && abs (b-y) <= 1 


isFullColor :: (Eq a1, Eq a2) => (a1, a1, a2, a2) -> Bool
isFullColor (r, g, b, y) = r == g &&  b == y

addColor :: (Num a1, Num a2, Num a3, Num a4) =>
   Char -> (a1, a2, a3, a4) -> (a1, a2, a3, a4)
addColor c (r, g, b, y) = case c of
  'R' ->  (r+1,g, b,y )
  'G' ->  (r, g+1, b, y)
  'B' ->  (r, g, b+1, y )
  'Y' ->  (r, g, b, y+1 )

colorSequence :: (Ord a2, Ord a4, Num a2, Num a4) =>
   [Char] -> (a2, a2, a4, a4) -> Bool
colorSequence [] (r, g, b, y) = isFullColor (r, g, b, y)
colorSequence (x:xs) (r, g, b, y)
  | isBalanceColor (r, g, b, y) = colorSequence xs (addColor x (r, g, b, y))
  | otherwise = False


main :: IO ()
main = do
  lines <- readLn :: IO Int
  replicateM_ lines (getLine >>= print . (`colorSequence` (0,0,0,0)))
````

Hierarchy Decomposition
---

Further Readings
---
###### tags: `Hacker Rank`

