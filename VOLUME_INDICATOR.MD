```
//@version=5
indicator('VSD')

//Volume

len = input.int(21, minval=1, title='Length')

Xm = ta.sma(volume, len)

X = volume

X1 = (X - Xm) * (X - Xm)

SD = math.sqrt(X1 / (len - 1))

barColorsOnPrevClose = input(title='Color bars based on previous close', defval=false)

palette = barColorsOnPrevClose ? close[1] > close ? color.orange : color.blue : open > close ? color.orange : color.blue

plot(volume, style=plot.style_histogram, color=palette)
```
