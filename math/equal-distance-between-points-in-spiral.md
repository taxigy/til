# Place points at equal distance from each other defined in polar coordinates

To paint a spiral with points, we need to know an increment of angle and an increment of radius. The problem is that as both angle and radius grow, the spiral gets "bigger". So the angle needs negative acceleration to slow down as the radius grows. But the radius itself also needs to decelerate. Both need to decelerate precisely at a square-root speed.

A simple generator for points then would be

```javascript
const points = (dr, dth) => function* () {
  let r = dr;
  let th = dth;

  while (true) {
    r += (dr ** 2 ) / r;
    th += (dth ** 2) / r;
    yield({ r, th });
  }
};
```

We usually need the same points but in Cartesian coordinates, so let's output a familiar `[x, y]` pair instead:

```javascript
const points = (dr, dth) => function* () {
  let r = dr;
  let th = dth;

  while (true) {
    r += (dr ** 2 ) / r;
    th += (dth ** 2) / r;
    yield([r * Math.cos(th), r * Math.sin(th)]);
  }
};
```

Now we can [take](../javascript/take-number-of-values-from-generator.md), say, 10 points from the generator:

```javascript
> for (let point of take(10, points(2, 2))) { console.log(point) }
< [ -3.9599699864017817, 0.5644800322394689 ]
< [ -3.9548385595720843, -3.0592894547135945 ]
< [ -1.2812009788530732, -5.656723791364214 ]
< [ 2.4890744602911474, -5.993340686792586 ]
< [ 5.807034734362828, -4.095592553566263 ]
< [ 7.636013637386004, -0.7097085498297718 ]
< [ 7.557622512199015, 3.157021377065825 ]
< [ 5.684866884955459, 6.557837744828935 ]
< [ 2.495642868384649, 8.792447482163597 ]
< [ -1.3467399132106848, 9.482256762062931 ]
```

See [a Codepen](https://codepen.io/rishatmuhametshin/pen/EOOrxL) that demonstrates this formula.
