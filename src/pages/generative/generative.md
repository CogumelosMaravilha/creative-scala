## Generative Art

```tut:invisible
import doodle.core._
import doodle.core.Image._
import doodle.syntax._
import doodle.jvm.Java2DCanvas._
import doodle.backend.StandardInterpreter._
```

Generative art means art where some part of the composition is determined by an autonomous process. Concretely, for us this will mean adding an element of randomness.

Let's take a really simple example. We've learned previously how to create concentric circles.

```tut:silent:book
def concentricCircles(n: Int): Image =
  n match {
    case 0 => circle(10)
    case n => concentricCircles(n-1) on circle(n * 10) 
  }
```

(We now know we could write this using a `Range` and a method like `allOn`.)

We also learned how we could make coloured circles, using a second parameter.

```tut:silent:book
def concentricCircles(n: Int, color: Color): Image =
  n match {
    case 0 => circle(10) fillColor color
    case n => concentricCircles(n-1, color.spin(15.degrees)) on (circle(n * 10) fillColor color) 
  }
```

Pictures constructed in this way are nice, but they are a bit boring in their regularity. What if we wanted to make a random alteration to the hue of the color at each step?

Scala provides some methods that produce *random numbers*. One such method is `math.random`. Each time we call it we get a different `Double` between 0.0 and 1.0[^pseudo-random]. 

[^pseudo-random]: These numbers are not truly random. The output is determined by a value known as the *seed*. If we know the seed we can perfectly predict all the result we'll get from calling `math.random`. However, going the other way---that is, predicting the seed given a sequence of outputs---is very difficult. The numbers so generated are called *pseudo-random numbers*, because they are not truly random but nonetheless are very difficult to predict.

```tut:book
math.random
math.random
```

Given `math.random` we could produce a method that returns a random `Angle` like so.

```tut:book
def randomAngle: Angle = 
  math.random.turns
  
randomAngle
randomAngle
```

Why might we not want to do this? What principle does this break?

<div class="solution">
Generating random numbers in this way breaks substitution. Remember substitution says wherever we see an expression we should be able to substitute the value it evaluates to without changing the meaning of the program. Concretely, this means

```tut:book
val result1 = randomAngle
val result2 = randomAngle
```

and 

```tut:book
val result1 = randomAngle
val result2 = result1
```

should be the same program and clearly they are not.
</div>

What should we do? Suffer the slings and arrows of outrageous computational models, or take arms against a sea of side-effects, and by opposing end them! There's really only one choice.
