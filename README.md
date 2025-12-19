# eTRU Metrics Dashboard

A comprehensive web-based dashboard for monitoring and analyzing electric Truck Refrigeration Unit (eTRU) charging operations at the Lineage Statesville facility.

## Overview

This dashboard provides real-time visibility into eTRU trailer activity, charging infrastructure utilization, and energy consumption metrics. It helps facility operators optimize eTRU trailer assignments, track charging performance, and analyze energy usage patterns.

## Features

### 📊 Key Metrics Sections

#### 1. **eTRU Entry**
- **Total Trailers**: Count of all trailer appointments
- **eTRU Trailers**: Number of eTRU-equipped trailers
- **eTRU-Enabled Trailer %**: Percentage of eTRU-capable trailers

#### 2. **eTRU Assignment**
- **Correct eTRU Assignments**: Trailers parked in charging-enabled spaces
- **eTRU Missed Assignments**: Trailers parked without charging access
- **eTRU Assignment Rate**: Percentage of correct assignments

#### 3. **Charging Time**
- **Assigned Charging Time**: Hours attributed to specific customers
- **Unassigned Charging Time**: Hours not attributed to customers
- **Total Charging Time**: Combined charging hours

#### 4. **Charging Sessions**
- **Number of Sessions**: Total valid charging sessions
- **Average Session Duration**: Mean length of charging sessions

#### 5. **Consumption**
- **Total Assigned Usage**: Energy consumption attributed to customers
- **Total Unassigned Usage**: Energy consumption not attributed to customers
- **Total Usage**: Facility-wide submeter energy consumption

### 🔍 Session Filtering

The dashboard automatically filters out invalid sessions:
- Sessions with **0 kWh usage**
- Sessions with **duration < 2 minutes**

This ensures accurate metrics by excluding noise from brief connections or inactive periods.

### 🚥 Pedestal Status Monitor

Real-time status indicators for each charging pedestal:
- **Green**: Active (currently drawing power)
- **Gray**: Inactive (no power draw)

Status updates based on the last 15 minutes of data.

### 📅 Date Range Selection

Flexible date filtering options:
- This Month
- Last Month
- Last 7 Days
- Last 30 Days
- Last 90 Days
- Year to Date
- Custom Range (select specific start/end dates)

Date selections are saved in browser localStorage for convenience.

### 📈 Interactive Visualizations

Click any metric card to view a detailed time-series chart showing how that metric has changed over the selected date range. Charts automatically adjust granularity:
- **Daily** buckets for ranges ≤ 90 days
- **Monthly** buckets for ranges 91-365 days
- **Yearly** buckets for ranges > 365 days

### 📥 Export Functionality

Two export options available:

#### Export Last 6 Months KPIs
Downloads a CSV file containing:
- All key metrics for the last 6 months
- Period information
- Generation timestamp
- Filtering notes

#### Export All Sessions for Selected Period
Downloads a detailed CSV file with:
- Session ID
- Submeter location
- Start/end timestamps
- Session duration
- Customer assignment
- Energy usage (kWh)

Only includes sessions that pass the filtering criteria.

## Technical Architecture

### Data Sources

The dashboard queries two GraphQL endpoints:

1. **Trailer Appointments API** (`/proxy`)
   - Trailer check-in data
   - Park session information
   - Space assignments and eTRU capabilities

2. **Submeter Sessions API** (`/proxy-submeter`)
   - Charging session data
   - Energy consumption metrics
   - Real-time submeter status
   - Customer associations

### Technology Stack

- **Frontend**: Vanilla JavaScript (no framework dependencies)
- **Visualization**: Chart.js
- **Styling**: Custom CSS with modern design patterns
- **Data Fetching**: Fetch API with GraphQL queries
- **Storage**: localStorage for preferences

### Key Components

#### Data Processing
- Time-based bucketing for aggregations
- Dynamic query generation based on date ranges
- Client-side filtering and calculations

#### UI Components
- Modal system for detailed charts
- Loading overlays with spinners
- Dropdown menus for exports
- Info tooltips with metric explanations
- Responsive card-based layout

## Setup & Usage

### Requirements

- Modern web browser (Chrome, Firefox, Safari, Edge)
- Internet connection (for API access and Chart.js CDN)
- Access to Lineage Statesville GraphQL endpoints

### Installation

1. Download the `etru-dashboard-updated.html` file
2. Open in a web browser
3. No build process or dependencies required!

### Running the Dashboard

Simply open the HTML file in a web browser. The dashboard will:
1. Restore your last selected date range (if any)
2. Automatically load metrics for that range
3. Display real-time pedestal status

### Usage Tips

- **Click any metric card** to see historical trends
- **Use date range selector** to analyze different time periods
- **Export data** for further analysis in Excel/spreadsheet tools
- **Monitor pedestal status** to see which charging stations are active
- **Check tooltips** (i icons) for detailed metric explanations

## Metrics Calculation Details

### Assignment Rate
```
Assignment Rate = (Correct eTRU Assignments / Total Yard Sessions) × 100
```

### eTRU-Enabled Trailer %
```
eTRU-Enabled % = (eTRU Trailers / Total Trailers) × 100
```

### Average Session Duration
```
Average Duration = Total Charging Time / Number of Valid Sessions
```

### Session Validity Criteria
A session is considered valid if:
- Energy usage > 0 kWh, AND
- Duration ≥ 2 minutes

## Data Updates

- **Metrics**: Refresh when date range changes
- **Pedestal Status**: Based on last 15 minutes of data
- **Real-time**: Dashboard queries live data on each load/filter change

## Browser Compatibility

Tested and supported on:
- Chrome 90+
- Firefox 88+
- Safari 14+
- Edge 90+

## Performance Considerations

- **Large Date Ranges**: Queries for year+ ranges may take longer to process
- **Session Count**: Thousands of sessions are filtered client-side
- **Chart Rendering**: Large datasets automatically use monthly/yearly buckets

## Troubleshooting

### Dashboard shows "Loading..." indefinitely
- Check browser console for API errors
- Verify network connection
- Ensure GraphQL endpoints are accessible

### Metrics show "Error"
- API may be temporarily unavailable
- Check date range validity (no future dates)
- Refresh the page to retry

### Pedestal status not updating
- Status based on recent data (last 24 hours)
- Verify submeters are reporting data
- Check console for submeter status query errors

### Export fails
- Ensure date range is selected
- Check that data loaded successfully
- Verify browser allows file downloads

## Data Privacy & Security

- No user data is collected or transmitted
- All processing happens client-side
- localStorage only stores date preferences
- API credentials handled server-side by proxy

## Customization

### Modifying Proxy Endpoints

Update the fetch URLs to point to your GraphQL endpoints:
```javascript
fetch('https://your-proxy-url.com/proxy', { ... })
fetch('https://your-proxy-url.com/proxy-submeter', { ... })
```

### Adjusting Session Filters

Modify the filter criteria in the session processing logic:
```javascript
const sessions = allSessions.filter(s => {
  const usage = s.usage?.aggregates?.SUM || 0;
  if (usage === 0) return false;  // Change usage threshold
  
  const durationMinutes = durationMs / (1000 * 60);
  if (durationMinutes < 2) return false;  // Change duration threshold
  
  return true;
});
```

### Styling

All styles are contained in the `<style>` block. Key CSS variables and classes:
- `.metric-card`: Individual metric cards
- `.header-banner`: Top navigation bar
- `.modal`: Chart overlay system
- Colors: `#2c7be5` (primary blue), `#2b2f32` (text)

## Support & Feedback

For issues or feature requests related to the dashboard functionality, contact the Nsight development team.

For facility-specific questions about eTRU operations, contact Lineage Statesville facility management.

## Version History

### v2.0 (Current)
- Added session filtering (0 usage and <2 min duration)
- Updated tooltips to reflect filtering
- Applied filters to export functions
- Added filtering note to KPI export

### v1.0
- Initial release with core metrics
- Interactive modal charts
- Dual export functionality
- Real-time pedestal status

## License

Copyright © 2024 ndustrial.io. All rights reserved.

---

**Dashboard Version**: 2.0  
**Last Updated**: December 2024  
**Maintained By**: Nsight Platform Team
