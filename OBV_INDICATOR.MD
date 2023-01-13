```
//@version=5
// Copyright (c) 2018-present, Alex Orekhov (everget)
// On Balance Volume script may be freely distributed under the MIT license.
indicator('Final OBV', shorttitle='OBV', precision=0)

showSignal = input(title='Show Signal ?', defval=true)
signalType = input.string(title='Signal Smoothing Type', defval='SMA', options=['EMA', 'SMA'])
signalLength = input(title='Signal Smoothing Length', defval=21)
highlightCrossovers = input(title='Highlight Crossovers ?', defval=false)
applyFilling = input(title='Apply Ribbon Filling ?', defval=true)
src = input(title='Source', defval=close)

//obv = cum(sign(change(src)) * volume)
ema_1 = ta.ema(ta.obv, signalLength)
sma_1 = ta.sma(ta.obv, signalLength)
signal = signalType == 'EMA' ? ema_1 : sma_1

trendColor = ta.obv > signal ? color.new(#fc9804, 0) : color.new(#0350f7, 0) 

obvColor = applyFilling ? trendColor : color.red
signalColor = applyFilling ? trendColor : color.green

obvPlot = plot(ta.obv, title='OBV', linewidth=2, color=obvColor, transp=0)
signalPlot = plot(showSignal ? signal : na, title='Signal', color=signalColor, transp=0)

transparent = color.new(color.white, 100)

fillColor = applyFilling ? trendColor : transparent
fill(obvPlot, signalPlot, color=fillColor, transp=70)

plotshape(ta.crossover(ta.obv, signal) and highlightCrossovers ? ta.obv : na, title='Crossover', location=location.absolute, style=shape.circle, size=size.tiny, color=color.new(#5787ee, 0))
plotshape(ta.crossunder(ta.obv, signal) and highlightCrossovers ? ta.obv : na, title='Crossunder', location=location.absolute, style=shape.circle, size=size.tiny, color=color.new(#f0b459, 0))

