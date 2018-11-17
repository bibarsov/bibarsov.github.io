---
published: true
layout: post
date: 2018-11-17T00:00:00.000Z
categories: java
---
## Solving a task during a job interview

There was an interesting task last time I got interviewed. Imagine we have a circle inside a square.
So it looks like the following:

![cartesian coordinates]({{site.baseurl}}/assets/img/unit_circle.png)

As the Monte Carlo method states, we can generate PI number by dividing the area of the circle by the area of the square and multiplying it by four.

Next description given:
There is a circle limited by raduis 1.
Circle's area is calculated by the formula: Pi * r^2 (in our case it's simply Pi)
Square's area is calculated by the formula: 4 * r^2 (it's simply 4)

So if we randomly generate dots on circle and square then it's relation produce the same value as the relation of their corresponding areas:
```
area(c) / area (s) = Pi / 4
```
And the question is how to find the Pi number using only preceding formula.

I've tried to provide a straightforward solution and it looks like the following:

```java

import java.math.BigDecimal;
import java.math.RoundingMode;
import java.util.HashSet;
import java.util.Random;

public class Main {
    public static void main(String[] args) {

        int precision = Integer.valueOf(args[0]);
        HashSet<String> strings = new HashSet<>();

        int circleCounter = 0;
        int squareCounter = 0;

        while (precision-- > 0) {
            double x = BigDecimal.valueOf(randomDouble()).setScale(4, RoundingMode.HALF_UP).doubleValue();
            double y = BigDecimal.valueOf(randomDouble()).setScale(4, RoundingMode.HALF_UP).doubleValue();

            if (strings.add(String.format("%s:%s", x, y))) {
                if ((x * x + y * y) <= 1) {
                    circleCounter += 1;
                    squareCounter += 1;
                } else {
                    squareCounter += 1;
                }
            }
        }
        System.out.println((circleCounter * 1.0 / squareCounter) * 4);
    }
    /** 
		Math.random by default has a range [0, 1)
    	So I somehow developed random generator of range [0, 1] 
    */
    private static Double randomDouble() {
        double rand1 = new Random().nextDouble();
        double rand2 = 1.0d - new Random().nextDouble();
        boolean randPicker = new Random().nextBoolean();
        if (randPicker) {
            return rand1;
        } else {
            return rand2;
        }
    }
}

```

Precision number there gives a number of iteration so we come closer to the true PI as we provide more iterations. To be honest I'm a little bit stuck with the scales of x and y. This code may seem ostensible, but at least it gets closest to the real PI number. I will appreciate your comments how we can adapt this code.