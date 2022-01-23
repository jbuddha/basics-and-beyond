---
title: Generating the Jam Coins
date: 2016-04-12
tags: ['problems', 'codejam', 'java', 'puzzle', 'competition', 'solution']
author: Buddha
description: I'm going to show you how to solve JamCoins problem of 2016 Google codejam for both small as well as large datasets within time limit.
sourcecode: https://github.com/jbuddha/competitions/blob/master/codejam/src/year2016/JamCoins.java
sourcetype: g
---

Here is a moderate problem of CodeJam's qualification round of 2016. The Jam Coins. It is an interesting problem which gives a glimpse into another popular virtual currency, bit-coin mining. Here is the description of the problem. You need to generate jamcoins of either 16 digits for small dataset or 32 digits for large datasets. Jam Coins follow the given rules...
1. A Jam Coin is only made up of 1's & 0's of the required number of digits.
1. It begins and ends with 1.
1. If that interpreted from base 2 to base 10, it should not be a prime number in any of them.

For Small dataset, you need to generate 50 jam coins of 16 digits and for large dataset, you need to generate 500 jam coins of 32 digits following above rules.
<!-- more -->
Output should be the list of Jam Coins where each is followed by a divisor of that number in each base.

Let us say that we want to test if `11001101` is a jam coin or not.

If we assume that the number is in base 2, it's decimal equivalent is 2^7+2^6+2^3+2^2+1 = 205 => Not a prime number => Divisible by 5
If we assume that the number is in base 3, it's decimal equivalent is 3^7+3^6+3^3+3^2+1 = 2953 => Prime Number => Hence not a jam coin

Let us test `1010101`

{% raw %}
<div style="width: 50%;">
{% endraw %}

|Base|Decimal Equivalent|Divisior|
|-|-|-|
|2| 85      |5 |
|3| 820     |2|
|4| 4369    |17|
|5| 16276   |2|
|6| 47989   |37|
|7| 120100  |2|
|8| 266305  |5|
|9| 538084  |2|
|10|1010101 |73|

{% raw %}
</div>
{% endraw %}

It is divisible by some or number in all bases from 2 to 10. Hence it is a Jam Coin. We need to generate such coins with given number of digits.

Hence this can be included in output as below if the input is `7 10`

```
Case #1:
1000001 5 2 17 2 13 2 5 2 101
1001011 3 2 5 2 7 2 3 2 11
1010101 5 2 17 2 37 2 5 2 73
1011101 3 7 11 3 5 43 3 11 7
1011111 5 2 3 2 37 2 5 2 3
1100011 3 2 5 2 7 2 3 2 11
1101001 3 2 5 2 7 2 3 2 11
1101111 3 2 3 2 7 2 3 2 3
1110111 7 2 3 2 43 2 17 2 3
1111011 3 2 3 2 7 2 3 2 3
```

## My solution
Let us begin with breaking the problem into manageable chunks before we try to solve it.

{% codeblock Solution Pseudo Code  %}
Generate a number with required number of digits
    - Convert it to decimal assuming it is base 2
    - Test if it is a prime number
    - If it is a prime number, go to step 1 else continue
    - Convert it to decimal assuming it is base 3
    - Test if it is a prime number
    - If it is a prime number, go to step 1 else continue
    - .... repeat till you reach base 10
Find Divisors in each base
Print the number
Print all divisors
{% endcodeblock %}

There are several complex problems inside the deceptively simple pseudocode

1. Handle large numbers. 16 digits are way too big for a long datatype.
1. Generate a number of required digits of 1's & 0's
2. Convert the number to decimal from given base
3. Testing if it is prime number
4. Finding a divisor

Let us solve them one by one

##### [1/5]Handling insanely large numbers
It depends on the programming language of your choice. For this solution, I have chosen Java, which has java.math.BigInteger class that can store numbers and provides a very useful methods for prime number calculations. Example usage is as below.
{% codeblock HelloWorldComponent.java lang:java  %}
BigInteger num = new BigInteger("101010110000011");
num.nextProbablePrime();
num.isProbablePrime(10);
num = new BigInteger("100001",3);   // converts 100001 to base 10 from base 3
num.toString(2);                    // converts the num to base 2 from base 10
{% endcodeblock %}

##### [2/5]Generating combination of 1s and 0s that begin and end with 1
Following is the algorithm I followed.
1. Generate a string of zeroes of size n-2, assuming n is the length required
1. Append 1 before and after the string of zeroes
1. To Generate another number, imagine the number if in binary, adding two(10 in binary format) will give next odd number. Any number ending with 1 in binary number is an odd number.

```
100000000001
           +
          10
------------
100000000011
           +
          10
------------
100000000101

```
How do you add 10 in binary format? Just convert that base 2 number to base 10 and add 2 and convert it back to binary number, which leads us to the following question.

##### [3/5]Convert the number to decimal from given base
```
BigInteger incrementInBinaryByTwo(BigInteger num)
{
	return new BigInteger(new BigInteger(num.toString(),2).add(ONE).add(ONE).toString(2));
}
```
Converting from a decimal number to binary format is as simple as calling toString method with 2 as the parameter.

##### [4/5]Testing if it is a prime number
BigInteger class of Java provides a nice API to work with prime numbers

BigInteger.isProbablePrime() will return false if it is definately not a prime and returns true, if the probablity for this number to be a prime number is less than 2^-100. Hence for our purpose of finding it is not a prime number this would serve the purpose.
```
static boolean isComposite(BigInteger num) {
    for (int base = 2; base < 11; base++) {
        if (new BigInteger(num.toString(), base).isProbablePrime(10)) {
            return false;
        }
    }
    return true;
}
```

##### [5/5]Finding a divisor
BigInteger has nextProbablePrime method that returns a prime number after the given number. So, if we use it on a prime number, we can get the next prime number and so on. As we can now get all prime numbers one after the another, we can divide our number with each prime number and return the first divisor. However, going indefinately till all primes are verified is inefficient for this problem. There will be simpler jam coins to mine. So, we will test for first 10000 prime numbers. If it is not divisible by any of it, we ignore that number and continue with next number. Here is the code.

```
static BigInteger findSmallestFactor(BigInteger n) {
    BigInteger half = sqrt(n);
    BigInteger factor = new BigInteger("2");
    int counter = 0;
    while (factor.compareTo(half) < 0 && !n.mod(factor).equals(BigInteger.ZERO)) {
        factor = factor.nextProbablePrime();
        counter++;
        if (counter == 10000) {
            break;
        }
    }
    if (!n.mod(factor).equals(BigInteger.ZERO)) {
        throw new RuntimeException();
    }
    return factor;
}
```

{% codeblock JamCoins.java lang:java  %}
package year2016;

import java.io.BufferedReader;
import java.io.FileReader;
import java.io.IOException;
import java.math.BigInteger;
import static java.math.BigInteger.ONE;
import java.nio.charset.Charset;
import java.nio.file.Files;
import java.nio.file.Path;
import java.nio.file.Paths;
import java.util.ArrayList;
import java.util.List;

public class JamCoins {

    static List<BigInteger> coins = new ArrayList<>();

    public static void processTest(String test) {

        int size = Integer.parseInt(test.split(" ")[0]);
        int count = Integer.parseInt(test.split(" ")[1]);

        List<BigInteger> jamCoins = new ArrayList<>();
        String zeroes = "";
        for (int i = 2; i < size; i++) {
            zeroes += "0";
        }

        BigInteger num = new BigInteger("1" + zeroes + "1");
        String entry = "";

        while (num.toString().length() == size && jamCoins.size() < count) {
            if (isComposite(num)) {
                try {
                    entry = num.toString();
                    for (int base = 2; base < 11; base++) {
                        entry += " " + findSmallestFactor(new BigInteger(num.toString(), base));
                    }
                    addOutput(entry);
                    jamCoins.add(num);
                } catch (Exception e) {
                }
            }

            num = incrementInBinaryByTwo(num);
        }
    }

    static BigInteger findSmallestFactor(BigInteger n) {
        BigInteger half = sqrt(n);
        BigInteger factor = new BigInteger("2");
        int counter = 0;
        while (factor.compareTo(half) < 0 && !n.mod(factor).equals(BigInteger.ZERO)) {
            factor = factor.nextProbablePrime();
            counter++;
            if (counter == 10000) {
                break;
            }
        }
        if (!n.mod(factor).equals(BigInteger.ZERO)) {
            throw new RuntimeException();
        }
        return factor;
    }

    static BigInteger sqrt(BigInteger n) {
        BigInteger div = BigInteger.ZERO.setBit(n.bitLength() / 2);
        BigInteger div2 = div;
        while (true) {
            BigInteger y = div.add(n.divide(div)).shiftRight(1);
            if (y.equals(div) || y.equals(div2)) {
                return y;
            }
            div2 = div;
            div = y;
        }
    }

    static boolean isComposite(BigInteger num) {
        for (int base = 2; base < 11; base++) {
            if (new BigInteger(num.toString(), base).isProbablePrime(10)) {
                return false;
            }
        }
        return true;
    }

    static final int max = 32;

    static BigInteger incrementInBinaryByTwo(BigInteger num) {
        return new BigInteger(new BigInteger(num.toString(), 2).add(ONE).add(ONE).toString(2));
    }

    static BigInteger decrementInBinary(BigInteger num) {
        return new BigInteger(new BigInteger(num.toString(), 2).subtract(ONE).toString(2));
    }

    // Fixed Code Begins *********
    private static int count = 0;
    private static final String INPUT_FILE = "input.txt";
    private static final Path OUTPUT_PATH = Paths.get("output.txt");

    private static final List<String> TESTS = new ArrayList<>();
    private static final List<String> OUTPUT = new ArrayList<>();

    public static void main(String[] args) throws IOException {
        OUTPUT.clear();
        OUTPUT.add("Case #1:");
        processInputFile();
        Files.write(OUTPUT_PATH, OUTPUT, Charset.defaultCharset());
    }

    public static void addOutput(String answer) {
        int c = OUTPUT.size() + 1;
        OUTPUT.add(answer);
    }

    private static void processInputFile() throws IOException {
        try (BufferedReader br = new BufferedReader(new FileReader(INPUT_FILE))) {
            String line;
            count = Integer.parseInt(br.readLine());
            while ((line = br.readLine()) != null) {
                processTest(line);
            }
        }
    }
}

{% endcodeblock %}
