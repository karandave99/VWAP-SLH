# Pine Script Libraries - Quick Reference

## ZigZagLib - Quick Reference

### Initialize
```pinescript
var htfZZ = zz.init(8)   // Higher timeframe zigzag
var ltfZZ = zz.init(2)   // Lower timeframe zigzag
```

### Update (call every bar)
```pinescript
directionChanged = htfZZ.update()  // Returns true if direction changed
```

### Get Pivot Data
```pinescript
// Current pivot (most recent)
currentPrice = htfZZ.getCurrentPrice()
currentBar = htfZZ.getCurrentBar()

// Previous pivot
previousPrice = htfZZ.getPreviousPrice()
previousBar = htfZZ.getPreviousBar()

// Specific pivot (0=current, 1=previous, 2=older...)
price = htfZZ.getPivotPrice(0)
barIndex = htfZZ.getPivotBar(0)
```

### Pattern Detection
```pinescript
pattern = htfZZ.getPattern()  // Returns "HH", "LL", "LH", "HL" or na

// Check direction
if htfZZ.direction == 1
    // Currently looking for highs (last pivot was low)
if htfZZ.direction == -1
    // Currently looking for lows (last pivot was high)
```

### Utility
```pinescript
hasData = htfZZ.hasEnoughPivots(3)  // Check if at least 3 pivots exist
```

---

## VWAPLib - Quick Reference

### Basic VWAP
```pinescript
vwapValue = vwap.vwap()                 // Standard daily VWAP
vwapValue = vwap.vwap(hlc3)            // VWAP with custom source
```

### Anchored VWAP
```pinescript
vwapValue = vwap.anchoredVWAP("Session", close)  // Daily anchor
vwapValue = vwap.anchoredVWAP("Week", close)     // Weekly anchor
vwapValue = vwap.anchoredVWAP("Month", close)    // Monthly anchor
vwapValue = vwap.anchoredVWAP("Year", close)     // Yearly anchor
```

### Cross Detection
```pinescript
crossedUp = vwap.crossAbove(vwapValue)      // Price crossed above VWAP
crossedDown = vwap.crossBelow(vwapValue)    // Price crossed below VWAP
```

### Position & Distance
```pinescript
pos = vwap.position(vwapValue)              // 1 (above), -1 (below), 0 (equal)
distPct = vwap.distancePercent(vwapValue)   // % distance from VWAP
distPoints = vwap.distance(vwapValue)       // Price point distance
```

### VWAP Bands
```pinescript
[upper, lower] = vwap.bands(vwapValue, 1.0)  // 1 std dev bands
[upper, lower] = vwap.bands(vwapValue, 2.0)  // 2 std dev bands
```

### VWAP Momentum
```pinescript
isUptrend = vwap.isTrendingUp(vwapValue, 5)    // VWAP rising for 5 bars
isDowntrend = vwap.isTrendingDown(vwapValue, 5) // VWAP falling for 5 bars
```

---

## LiquidityLib - Quick Reference

### Initialize Manager
```pinescript
var liquidityManager = liq.init(10)  // Max 10 active zones
```

### Create Liquidity Zone
```pinescript
// Buy liquidity (support)
zone = liquidityManager.createZone(
    price = low[5],              // Liquidity price level
    startBar = bar_index,        // Current bar (where VWAP crossed)
    pivotBar = bar_index - 5,    // Bar where pivot occurred
    isHigh = false,              // false = support (buy liquidity)
    labelText = "BUY LIQ",       // Label text
    lineColor = color.green      // Line/label color
)

// Sell liquidity (resistance)
zone = liquidityManager.createZone(
    price = high[8],             // Liquidity price level
    startBar = bar_index,        // Current bar (where VWAP crossed)
    pivotBar = bar_index - 8,    // Bar where pivot occurred
    isHigh = true,               // true = resistance (sell liquidity)
    labelText = "SELL LIQ",      // Label text
    lineColor = color.red        // Line/label color
)
```

### Add Confirmation Level
```pinescript
if not na(zone)
    zone.addConfirmation(
        price = low[2],              // Confirmation level price
        barIndex = bar_index - 2,    // Where confirmation occurred
        labelText = "LC",            // Label text
        lineColor = color.green      // Color
    )
```

### Check Zone Status
```pinescript
isTouched = zone.isTouched()  // Has price reached the zone?
```

### Update All Zones (call every bar)
```pinescript
liquidityManager.updateAll()  // Updates labels, checks for touches, removes touched zones
```

### Manager Operations
```pinescript
activeCount = liquidityManager.getActiveCount()  // Number of active zones
liquidityManager.clearAll()                       // Remove all zones
```

### Manual Zone Operations
```pinescript
zone.updateLabelPosition()          // Move label to current bar
zone.updateConfirmationLabel()      // Move confirmation label
zone.deactivate()                   // Stop extending lines
```

---

## Common Patterns

### Pattern 1: VWAP Liquidity Detection
```pinescript
// Initialize
var htfZZ = zz.init(8)
var liquidityManager = liq.init(10)
vwapValue = vwap.vwap()

// Update zigzag
htfZZ.update()

// Detect VWAP cross and create liquidity zone
if vwap.crossAbove(vwapValue) and htfZZ.hasEnoughPivots(1)
    lastLowPrice = htfZZ.direction == 1 ? htfZZ.getCurrentPrice() : htfZZ.getPreviousPrice()
    lastLowBar = htfZZ.direction == 1 ? htfZZ.getCurrentBar() : htfZZ.getPreviousBar()
    liquidityManager.createZone(lastLowPrice, bar_index, lastLowBar, false, "BUY LIQ", color.green)

// Update zones
liquidityManager.updateAll()
```

### Pattern 2: Dual Timeframe ZigZag
```pinescript
// Initialize
var htfZZ = zz.init(8)   // Major swings
var ltfZZ = zz.init(2)   // Minor swings

// Update both
htfChanged = htfZZ.update()
ltfChanged = ltfZZ.update()

// Use HTF for liquidity zones
// Use LTF for confirmation levels
if ltfChanged and ltfZZ.hasEnoughPivots(1)
    confirmationPrice = ltfZZ.getCurrentPrice()
    // Add to existing zone...
```

### Pattern 3: HH/LL Pattern Trading
```pinescript
var myZZ = zz.init(10)
myZZ.update()

pattern = myZZ.getPattern()

if pattern == "HH" and vwap.position(vwapValue) == 1
    // Higher high above VWAP = bullish continuation

if pattern == "LL" and vwap.position(vwapValue) == -1
    // Lower low below VWAP = bearish continuation
```

---

## Tips & Best Practices

### ZigZag Periods
- **HTF (5-20)**: Major market structure, swing points
- **LTF (1-5)**: Confirmation levels, entry/exit points
- HTF should always be > LTF

### VWAP Anchors
- **Session**: Day trading, intraday mean reversion
- **Week**: Swing trading, weekly bias
- **Month**: Position trading, major levels

### Liquidity Zones
- Limit max zones to prevent hitting Pine Script limits (500 lines/labels)
- Clean up touched zones promptly
- Use different colors for buy/sell liquidity

### Performance
- Only create lines/labels when needed
- Use `hasEnoughPivots()` before accessing pivot data
- Update labels on active zones only

---

## Error Handling

### Common Issues

**"Cannot read from array, index out of bounds"**
```pinescript
// Always check before accessing
if htfZZ.hasEnoughPivots(2)
    price = htfZZ.getPreviousPrice()  // Safe
```

**"Study references too many drawings"**
```pinescript
// Reduce max zones
var liquidityManager = liq.init(5)  // Instead of 50
```

**"Library not found"**
```pinescript
// Make sure libraries are published
// Update username in imports
import YourActualUsername/ZigZagLib/1 as zz
```
