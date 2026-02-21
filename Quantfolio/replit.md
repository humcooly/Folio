# FolioForge - Portfolio Construction & Backtesting Tool

## Overview
FolioForge (formerly QuantFolio AI) is a modern portfolio construction and backtesting web application. Users can build custom portfolios of stocks and ETFs, visualize allocations, and simulate historical performance with AI-powered insights using real market data.

## Project Architecture

### Technology Stack
- **Frontend Framework**: React 19 with TypeScript
- **Build Tool**: Vite 6
- **Backend**: Express.js server for Yahoo Finance API proxy
- **Database**: PostgreSQL (Neon) with Drizzle ORM
- **UI Components**: Custom components with Lucide icons
- **Charts**: Recharts for data visualization
- **AI Integration**: Google Gemini AI for portfolio analysis and asset search
- **Market Data**: Yahoo Finance (via yahoo-finance2 library)

### Project Structure
```
/
├── components/          # React UI components
│   ├── AllocationChart.tsx
│   ├── AssetCard.tsx
│   ├── BacktestChart.tsx
│   ├── CorrelationMatrix.tsx
│   └── ThemesPage.tsx    # Investment themes database with search
├── server/             # Backend API server
│   ├── index.ts        # Express server (port 3001) - Yahoo Finance + Portfolio CRUD
│   └── db.ts           # Database connection using Drizzle ORM
├── shared/             # Shared types and schemas
│   └── schema.ts       # Drizzle schema for portfolios
├── services/           # Business logic and API services
│   ├── geminiService.ts   # Google Gemini AI integration
│   └── marketService.ts   # Portfolio backtesting with real Yahoo Finance data
├── App.tsx            # Main application component
├── index.tsx          # Application entry point
├── types.ts           # TypeScript type definitions
├── vite.config.ts     # Vite configuration (with proxy to backend)
├── drizzle.config.ts  # Drizzle ORM configuration
└── package.json       # Node.js dependencies
```

### Key Features
- **Tab Navigation**: Switch between Portfolio builder and Themes database
- **Investment Themes**: Browse 25+ curated investment themes with search and alphabetical filter
- **Asset Search**: AI-powered search for stocks and ETFs
- **Portfolio Builder**: Add assets and manage allocations
- **Backtesting with Real Data**: Historical performance using actual Yahoo Finance prices
- **Return Type Toggle**: Switch between Total Returns (with dividends) and Price-only Returns
- **Benchmark Comparison**: Compare against major indices (SPY, QQQ, etc.)
- **DCA Simulation**: Dollar-cost averaging calculator
- **AI Analysis**: Market context and risk analysis via Gemini
- **Portfolio Management**: Save and load multiple portfolios
- **Interactive Tooltips**: Hover over metrics for plain-language explanations with actual calculated values
- **Correlation Matrix**: Shows diversification score and asset correlations
- **Drawdown Visualization**: Portfolio line turns red during maximum drawdown periods

## Configuration

### Environment Variables
- `GEMINI_API_KEY`: Required for AI-powered features (asset search, analysis, benchmark suggestions)
- `FRED_API_KEY`: Optional - enables live economic cycle data from Federal Reserve (get free key at https://fred.stlouisfed.org/docs/api/api_key.html)
- `ALPHA_VANTAGE_API_KEY`: Optional, available but not currently used (Yahoo Finance is primary data source)

### Development Servers
- **Frontend (Vite)**: Port 5000 (configured for Replit)
- **Backend (Express)**: Port 3001 (Yahoo Finance API proxy)
- **Host**: 0.0.0.0
- **HMR**: Configured for Replit's proxy environment

## Recent Changes
- **2025-11-30**: Theme Detail Page & Enhanced UX
  - Clicking a theme now opens a detailed page showing:
    - Theme header with name, icon, and description
    - Performance chart showing basket performance (1M/3M/6M/1Y timeframes)
    - Ranked list of all stocks with trend sparklines, prices, and evidence descriptions
    - "Add All to Portfolio" button creates new portfolio with all theme stocks
  - Fixed benchmark metrics display - removed truncation so all values are fully visible
  - Economic cycle now shows error state when FRED API key unavailable (no more mock data)

- **2025-11-30**: Tab Navigation & Investment Themes Database
  - Added horizontal tab navigation with Portfolio and Themes tabs
  - Created ThemesPage component with searchable investment themes database
  - 40+ curated investment themes including AI, Clean Energy, Cybersecurity, Healthcare, etc.
  - Alphabetical quick filter (A-Z) for rapid theme discovery
  - Featured themes carousel highlighting popular investment strategies
  - Each theme displays description, number of stocks, and sample tickers

- **2025-11-30**: Enhanced Economic Cycle Model with CEI and 6-Month Changes
  - Added CEI (Coincident Economic Index) from Philadelphia Fed (USPHCI series)
  - Now calculates 6-month annualized percentage change for both LEI and CEI
  - Enhanced scoring system with 6 metrics: LEI absolute, LEI change, CEI absolute, CEI change, GDP growth, unemployment change
  - Score breakdown included in API response for transparency
  - Phase thresholds adjusted: Expansion (>=5), Recovery (>=1), Slowdown (>=-3), Recession (<-3)
  - Results now align with IBKR TWS economic cycle assessment

- **2025-11-30**: New Features - Rebalancing, Economic Cycle & Enhanced Metrics
  - Added rebalancing toggle with frequency dropdown (Monthly, Quarterly, Semi-Annually, Annually)
  - Rebalancing simulation resets share allocations to target weights at specified intervals
  - Enhanced benchmark metrics display: larger font sizes and better spacing
  - Added Economic Cycle regime classification widget with interactive visualization
  - Economic cycle shows 4 phases: Recovery, Expansion, Slowdown, Recession
  - Moved economic cycle chart from right side to left sidebar (under allocation pie chart)
  - Updated phase data to match IBKR TWS format with historical date ranges for non-current phases
  - Modal shows "USA has been in X phase for Y days" for current phase, "USA was last in X phase from DATE - DATE" for historical phases
  - Clickable phases open detailed modal popup with causes and recommended asset types
  - Fixed drawdown chart coloring: single line changes color (blue/red) instead of overlay

- **2025-11-28**: UI Polish & Financial Accuracy Improvements
  - Fixed metric tooltip positioning: first/last cards use position props to prevent edge clipping
  - Optimized drawdown coloring: O(n) single-pass algorithm, shows red when >10% from peak OR below starting balance
  - Replaced saved portfolio modal with inline dropdown featuring click-outside-to-close behavior
  - Added chart legend distinguishing portfolio from benchmark
  - Fixed YTD calculation: uses explicit January 1 start date via backend ytd=true parameter
  - Redesigned return type toggle as compact sliding on/off switch beneath timeframe bar
  - Dynamic risk-free rate: fetches ^TNX ticker from Yahoo Finance for accurate Sharpe ratio calculations

- **2025-11-28**: Return Type Toggle, Metrics Fixes & UI Improvements
  - Added toggle to switch between Total Returns (includes dividends) and Price-only Returns
  - Fixed Sharpe Ratio calculation: now uses CAGR minus risk-free rate divided by annualized volatility
  - Fixed drawdown period coloring: uses raw ISO dates for accurate date comparison
  - Fixed metric tooltip hover: tooltips now properly appear on hover
  - Replaced saved portfolio inline list with a cleaner plus button + modal pattern
  - Fixed startup timing: backend now starts 2 seconds before frontend to avoid connection errors

- **2025-11-27**: Bug Fixes & Delete Portfolio Feature
  - Added delete portfolio functionality with confirmation dialog
  - Fixed Yahoo Finance library initialization (requires new class instantiation)
  - Upgraded Node.js from 20 to 22 (required by yahoo-finance2 library)
  - Fixed blank screen when loading portfolios with null assets

- **2025-11-27**: Backend Persistence & Portfolio as Asset/Benchmark
  - Added PostgreSQL database for portfolio persistence (portfolios survive page refresh)
  - Portfolios can now be added as assets in other portfolios
  - Saved portfolios can be selected as benchmarks for comparison
  - Fixed API connection to use Vite proxy (resolved "unable to load market data" error)
  - Added CRUD API endpoints for portfolio management
  - Updated frontend to fetch/save portfolios via backend API

- **2025-11-27**: Yahoo Finance Integration & UI Improvements
  - Integrated Yahoo Finance for real historical price data (no more mock data)
  - Created backend Express server to proxy Yahoo Finance requests (CORS workaround)
  - Updated backtesting to use actual adjusted close prices
  - Added "Real Data" / "Simulated" indicator badge on chart
  - Fixed chart legend to show only Portfolio and Benchmark (removed Max Drawdown from legend)
  - Enhanced metric tooltips with contextualized explanations including actual values
  - Max Drawdown tooltip now includes number of days spent in drawdown
  - Added loading spinner while fetching market data
  - Added error handling for failed data fetches

- **2025-11-27**: Initial Replit setup
  - Configured Vite to run on port 5000 for Replit compatibility
  - Set up HMR for Replit's proxy environment
  - Created workflow for frontend development server
  - Installed all Node.js dependencies

## Development Workflow

### Running Locally
The development server starts automatically via the "Frontend Server" workflow, which runs:
```bash
npm run dev
```
This uses concurrently to start both:
- `npm run server` - Express backend on port 3001
- `npm run client` - Vite frontend on port 5000

### Building for Production
```bash
npm run build
```

### Preview Production Build
```bash
npm run preview
```

## User Preferences
- User prefers real market data over simulated/mock data
- User wants tooltips with plain language explanations for general audiences
- User wants Max Drawdown to include the number of days in drawdown

## Notes
- The app requires a Gemini API key to function properly for AI features
- Yahoo Finance data is fetched via backend proxy to avoid CORS issues
- Backtesting uses adjusted close prices for accurate total return calculations
- The correlation matrix rewards negative correlations (good for hedging)
