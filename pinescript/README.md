# Pine Script Library System - VWAP Liquidity

A modular Pine Script library system for building VWAP-based liquidity detection indicators and strategies.

## Project Structure

```
pinescript/
├── libraries/              # Reusable library modules
│   ├── ZigZagLib.pine     # ZigZag pivot detection and management
│   ├── VWAPLib.pine       # VWAP calculations and utilities
│   └── LiquidityLib.pine  # Liquidity zone management
│
├── indicators/            # Visual indicators (use libraries)
│   └── VWAPLiquidityDetector.pine
│
└── strategies/            # Trading strategies (coming soon)
    └── VWAPLiquidityStrategy.pine
```

## Libraries

### 1. ZigZagLib.pine

**Purpose:** Detects pivot points and manages zigzag calculations

**Key Features:**
- Proper pivot detection using `ta.highestbars()` and `ta.lowestbars()`
- Updates pivots when higher highs or lower lows are found
- Stores pivot history in arrays for pattern detection
- Detects HH/LL/LH/HL patterns

**Main Types:**
- `ZigZagData` - Stores zigzag state and pivot history

**Key Functions:**
```pinescript
zz.init(period)                    // Initialize zigzag
zz.update()                        // Update on each bar
zz.getCurrentPrice()               // Get current pivot price
zz.getCurrentBar()                 // Get current pivot bar
zz.getPreviousPrice()              // Get previous pivot price
zz.getPattern()                    // Get HH/LL/LH/HL pattern
zz.hasEnoughPivots(minPivots)      // Check if enough pivots exist
```

### 2. VWAPLib.pine

**Purpose:** VWAP calculations and cross detection

**Key Features:**
- Standard VWAP calculation
- Anchored VWAP (Session, Week, Month, Year)
- Cross detection (above/below)
- Distance calculations
- VWAP bands and momentum

**Key Functions:**
```pinescript
vwap.vwap()                        // Standard VWAP
vwap.anchoredVWAP(anchor, src)     // Anchored VWAP
vwap.crossAbove(vwapValue)         // Detect cross above
vwap.crossBelow(vwapValue)         // Detect cross below
vwap.position(vwapValue)           // Get position relative to VWAP
vwap.distancePercent(vwapValue)    // Distance in percentage
```

### 3. LiquidityLib.pine

**Purpose:** Manage liquidity zones with lines and labels

**Key Features:**
- Track multiple liquidity zones
- Automatic label position updates
- Confirmation level management
- Zone touch detection
- Automatic cleanup when max zones reached

**Main Types:**
- `LiquidityZone` - Single liquidity level with line/label
- `LiquidityManager` - Manages multiple zones

**Key Functions:**
```pinescript
liq.init(maxZones)                                    // Initialize manager
manager.createZone(price, startBar, pivotBar, ...)    // Create new zone
zone.addConfirmation(price, barIndex, ...)            // Add confirmation line
zone.isTouched()                                      // Check if touched
manager.updateAll()                                   // Update all zones
manager.getActiveCount()                              // Get zone count
```

## Indicators

### VWAPLiquidityDetector.pine

**Purpose:** Detects VWAP-based liquidity zones using HTF/LTF zigzag pivots

**How It Works:**

1. **HTF ZigZag** (default: 8 period) - Identifies major swing points
2. **LTF ZigZag** (default: 2 period) - Identifies minor swing points
3. **VWAP Cross Detection**:
   - Cross **above** VWAP → Creates **buy liquidity** at last HTF pivot low
   - Cross **below** VWAP → Creates **sell liquidity** at last HTF pivot high
4. **Liquidation Confirmation (LC)**:
   - After cross up: Waits for LTF pivot high (confirmation level above)
   - After cross down: Waits for LTF pivot low (confirmation level below)
5. **Zone Deactivation**: Zones stop extending when price retouches them

**Inputs:**
- HTF/LTF ZigZag periods
- VWAP anchor (Session/Week/Month)
- Max active liquidity zones
- Colors for all elements

**Alerts:**
- VWAP Cross Up
- VWAP Cross Down

## Installation & Usage

### Step 1: Publish Libraries to TradingView

1. Open TradingView Pine Editor
2. Copy the content of each library file
3. Publish as a library (make public or private)
4. Note the library path (username/LibraryName/version)

### Step 2: Update Import Statements

In `VWAPLiquidityDetector.pine`, update the import statements with your username:

```pinescript
import YourUsername/ZigZagLib/1 as zz
import YourUsername/VWAPLib/1 as vwap
import YourUsername/LiquidityLib/1 as liq
```

### Step 3: Add Indicator to Chart

1. Copy `VWAPLiquidityDetector.pine` to Pine Editor
2. Add to chart
3. Configure inputs as needed

## Development Workflow

### Creating New Indicators

1. Import required libraries
2. Initialize library objects
3. Use library functions for calculations
4. Focus on indicator-specific logic only

Example:
```pinescript
//@version=6
indicator("My Custom Indicator", overlay=true)

import YourUsername/ZigZagLib/1 as zz
import YourUsername/VWAPLib/1 as vwap

var myZZ = zz.init(10)
vwapValue = vwap.vwap()

myZZ.update()
// ... your custom logic
```

### Creating Strategies

Strategies will import both libraries and indicators:

```pinescript
//@version=6
strategy("My Strategy", overlay=true)

import YourUsername/ZigZagLib/1 as zz
import YourUsername/VWAPLib/1 as vwap
import YourUsername/LiquidityLib/1 as liq

// Strategy logic here
```

## Next Steps

1. **Test Libraries** - Publish and test each library individually
2. **Debug Indicator** - Test the VWAP Liquidity Detector on different timeframes
3. **Build Strategies** - Create trading strategies using the libraries
4. **Add More Libraries**:
   - OrderBlockLib - Order block detection
   - FVGLib - Fair value gap detection
   - StructureLib - Market structure (BOS, CHoCH)

## Troubleshooting

### Library Import Errors

If you get import errors:
- Ensure libraries are published on TradingView
- Check username and version numbers match
- Libraries must be published before indicators can import them

### ZigZag Not Appearing

- Check if periods are appropriate for your timeframe
- HTF period should be larger than LTF period
- Try different period values (HTF: 5-20, LTF: 2-5)

### Too Many Lines/Labels

- Reduce "Max Active Liquidity Zones" input
- Libraries automatically clean up old zones

## License

Mozilla Public License 2.0

## Contributing

When adding new features:
1. Keep libraries focused and modular
2. Use proper type definitions
3. Add comprehensive comments
4. Test on multiple timeframes
5. Update this README
