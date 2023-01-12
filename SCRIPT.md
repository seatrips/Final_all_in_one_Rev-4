```
// This source code is subject to the terms of the Mozilla Public License 2.0 at https://mozilla.org/MPL/2.0/

// Price Change
// © domsito

// Directional Sentiment
// © Steversteves

// The Rest
// © Seatrips

// Inspiration
// BitcoinSmartMoney & BitcoinLive

//@version=5
indicator("Final DS S&R EMA SMA ROC PC rev 4", shorttitle="FDS", overlay=true)

lengthInput = input.int(150, "Length", minval = 2)

// Determine variables 

hi = ta.sma(high, lengthInput)
lo = ta.sma(low, lengthInput)
cl = ta.sma(close, lengthInput) 
op = ta.sma(open, lengthInput) 

// Roc add

roc = ((close - close[13]) / close[13]) * 100

// Lowest vs highest

lowest = ta.lowest(lo, lengthInput)
highest = ta.highest(hi, lengthInput)

// bools for ribbon 

bool closeabove = close >= cl 
bool closebelow = close <= cl 
bool highabove = high >= hi 
bool lowabove = low >= lo 
bool openabove = open >= op 

// bools for highest/lowest 

bool paabovehigh = close > highest 
bool pabelowlow = close < lowest 
bool middle = close >= lo and close <= hi

// Colours 

color bull = color.new(#fc9804, 0)
color bear = color.new(#0350f7, 0)
color neutral = color.rgb(231, 8, 8)
color standard = color.rgb(10, 241, 10)
color fillcolorbull = color.new(color.green, 88)
color fillcolorbear = color.new(color.red, 88)
color fillcolorneutral = color.new(color.gray, 88)

// Determine Colour 

color hicolor = highabove ? bull : bear 
color locolor = lowabove ? bull : bear 
color opcolor = openabove ? bull : bear 
color clcolor = closeabove ? bull : bear 
color lowestcolor = pabelowlow ? neutral : standard
color highestcolor = paabovehigh ? standard : neutral
color fillfinal = closeabove ? fillcolorbull : closebelow ? fillcolorbear : middle ? fillcolorneutral : na 

// Band Plots 

plot(lowest, "RorS", color = lowestcolor, linewidth=4)
plot(highest, "RorS", color = highestcolor, linewidth=4)

confirmOpen = input(true, title="Require Open above/below Close for Confirmation")

//signal once start

var isLong = false
var isShort = false

//Long

long = not isLong and (close > hi) and (roc > 0 and close[1] < close ? 1 : 0)

//Short

short = not isShort and (close < lo) and (roc < 0 and close[1] > close ? 1 : 0)

if (long)
    isLong := true
    isShort := false

if (short)
    isLong := false
    isShort := true

//signal once end

// Determine Colour 

color candlecolor = closeabove and not long ? bull : closebelow and not short ? bear : middle ? neutral : long ? standard : short ? neutral: na

// Set candle colors

barcolor(candlecolor)


//plot signals on chart after confirmation

plotshape(long and (not confirmOpen or close > open ), color=color.rgb(10, 241, 10), style=shape.arrowup, textcolor=color.rgb(10, 241, 10), title="Buy Signal", text="LONG", location=location.belowbar)
plotshape(short and (not confirmOpen or close < open), color=color.rgb(231, 8, 8), style=shape.arrowdown, textcolor=color.rgb(231, 8, 8), title="Sell Signal", text="SHORT", location=location.abovebar)

//create alerts for buy and sell signals

alertcondition(long and (not confirmOpen or close > open), title="Buy Signal Alert", message="A buy signal has been detected and to be more safe you better wait for next candle confirmation.Check slope of moving averages")
alertcondition(short and (not confirmOpen or close < open), title="Sell Signal Alert", message="A sell signal has been detected and to be more safe you better wait for next candle confirmation.Check slope of moving averages")

// show the buy and sell signals

bgcolor(long and (not confirmOpen or close > open) ? color.rgb(7, 250, 15, 91) : na, title="background buy confirmed")
bgcolor(short and (not confirmOpen or close < open) ? color.rgb(255, 153, 0, 92) : na, title="background sell confirmed")

//make labels show up next to lines

src = input(close, title='Source')
showlabels = input(title='Show Labels ?', defval=true)
offset_val = input(title='Label Offset', defval=0)

// Calculate support and resistance levels

resistance = ta.highest(high, 230)
support = ta.lowest(low, 230)

// Plot support and resistance lines

plot(resistance, color=color.new(#8a8484, 0), title='resistance', linewidth=1)
plot(support, color=color.new(#4b4d4b, 0), title='support', linewidth=1)

//plot EMA SMA

ema10plot = input(true, title='Show EMA10 on chart')
sma20plot = input(true, title='Show SMA20 on chart')
ema34plot = input(true, title='Show EMA34 on chart')
sma50plot = input(true, title='Show SMA50 on chart')
sma200plot = input(true, title='Show SMA200 on chart')

//len EMA SMA

len10 = input.int(10, minval=1, title='EMA 10 Length')
len20 = input.int(20, minval=1, title='SMA 20 Length')
len34 = input.int(34, minval=1, title='EMA 34 Length')
len50 = input.int(50, minval=1, title='SMA 50 Length')
len200 = input.int(200, minval=1, title='SMA 200 Length')

//EMA 10

out10 = ta.sma(src, len10)
up10 = out10 > out10[1]
down10 = out10 < out10[1]
mycolor10 = up10 ? color.rgb(4, 146, 2) : down10 ? color.rgb(4, 166, 2) : color.rgb(4, 186, 2)
plot(out10 and ema10plot ? out10 : na, title='EMA10', color=mycolor10, style=plot.style_line, linewidth=1)

//SMA 20

out20 = ta.sma(src, len20)
up20 = out20 > out20[1]
down20 = out20 < out20[1]
mycolor20 = up20 ? color.rgb(224, 64, 251) : down20 ? color.rgb(224, 84, 251) : color.rgb(233, 104, 223)
plot(out20 and sma20plot ? out20 : na, title='SMA20', color=mycolor20, style=plot.style_line, linewidth=1)

//EMA 34

out34 = ta.ema(src, len34)
up34 = out34 > out34[1]
down34 = out34 < out34[1]
mycolor34 = up34 ? color.rgb(251, 120, 80) : down34 ? color.rgb(251, 140, 80) : color.rgb(251, 160, 80)
plot(out34 and ema34plot ? out34 : na, title='EMA34', color=mycolor34, style=plot.style_line, linewidth=1)

//SMA 50

out50 = ta.sma(src, len50)
up50 = out50 > out50[1]
down50 = out50 < out50[1]
mycolor50 = up50 ? color.rgb(155, 70, 5) : down50 ? color.rgb(182, 90, 5) : color.rgb(207, 100, 6)
plot(out50 and sma50plot ? out50 : na, title='SMA50', color=mycolor50, style=plot.style_circles, linewidth=2)

//SMA 200

out200 = ta.sma(src, len200)
up200 = out200 > out200[1]
down200 = out200 < out200[1]
mycolor200 = up200 ? color.rgb(6, 90, 159) : down200 ? color.rgb(6, 110, 159) : color.rgb(6, 130, 159)
plot(out200 and sma200plot ? out200 : na, title='SMA200', color=mycolor200, style=plot.style_circles, linewidth=2)

//Movement Alerts

max_bars_back(time, 5000)

var bool showDailyChange = input.bool(true, "Show change since previous day close", group = "General")

var bool showPrevDayLine = input.bool(true, "Prevous day's closing line", group = "line", inline = "0")
var lineColor = input(color.blue, "", inline = "0", group = "line")

var bool showPrevWeekCloseLine = input.bool(true, "Prevous week's closing line", group = "line", inline = "1")
var lineColor2 = input(color.lime, "", inline = "1", group = "line")

var float alertDelta = input.float(5.0, "Alert delta", 0.01, 10000, tooltip = "Alerts will be triggered only after price changes by 'delta' amount from the last alert price")
var int minSeconds = input.int(60, "Minimum number of seconds between alerts", 10, 10000)

varip float lastAlertValue = 0.0
varip int lastAlertTime = 0

var lbl = label.new(na, na, na, style = label.style_label_left)
var lineDay = line.new(na, na, na, na, color = lineColor, style = line.style_solid, width = 2)
var lineWeek = line.new(na, na, na, na, color = lineColor2, style = line.style_solid, width = 2)

varip float change = 0.0
varip float changeSign = na

delta(float v1, float v2) => 
    (((v2 / v1)-1) * 100.0)


var float prevDayClose = na
var int bar_time_prev_day_close = 0
var int bar_time_prev_week_close = 0
var float prevWeekClose = 0

if (ta.change(dayofmonth))   
    prevDayClose := close[2] //previous bar close is previous day's closing price, since we just entered after hours        
    bar_time_prev_day_close := bar_index[2]

if (ta.change(weekofyear))
    bar_time_prev_week_close := bar_index[2]
    prevWeekClose := close[2]

if barstate.islast 
    change := delta(prevDayClose, close)
    backColor = change < 0.0 ? color.red : color.green 
    if showPrevDayLine
        line.set_x1(lineDay, bar_time_prev_day_close )
        line.set_x2(lineDay, bar_index)
        line.set_y1(lineDay, prevDayClose) 
        line.set_y2(lineDay, prevDayClose)
    if showPrevWeekCloseLine
        line.set_x1(lineWeek, bar_time_prev_week_close)
        line.set_x2(lineWeek, bar_index)
        line.set_y1(lineWeek, prevWeekClose)
        line.set_y2(lineWeek, prevWeekClose)
    if showDailyChange
        label.set_text(lbl, str.tostring(change, "0.00") + "% ")
        label.set_color(lbl, backColor)
        label.set_x(lbl, bar_index+1)
        label.set_y(lbl, close)

if (barstate.islastconfirmedhistory)
    change := delta(prevDayClose, close)
    changeSign := math.sign(change)
    lastAlertValue := math.round(close / alertDelta) * alertDelta

if (math.abs(close-lastAlertValue) >= alertDelta) and ((time - lastAlertTime)/1000.0 >= minSeconds)
    alert(syminfo.ticker + ((close-lastAlertValue) > 0 ? " UP" : " DOWN") + " to " + str.tostring(close, "0.00"), alert.freq_all)
    //lastAlertValue:= close
    lastAlertValue := math.round(close / alertDelta) * alertDelta
    lastAlertTime := time

plot(lastAlertValue, "last alert value", display = display.data_window)
plot((time - lastAlertTime)/1000.0, "Seconds since last alert", display = display.data_window)
```


