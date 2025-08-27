# Store Monitoring System
A backend solution for monitoring restaurant uptime/downtime based on sparse polling data.

## Problem Statement
Restaurant monitoring system that calculates uptime/downtime reports from:
- Hourly polling data (active/inactive status)
- Business hours information (local timezone)
- Store timezone mappings

**Challenge**: Transform sparse polling observations into continuous uptime/downtime calculations for entire business hours.

## Solution Approach

**Interpolation Algorithm:**
to solve the sparse data problem, we use
def calculate_uptime_downtime():
- Get business intervals for each day
- Find polling data within each interval  
- Build timeline: [start] -> [polls] -> [end]
- Forward-fill status between consecutive points
- Sum up active/inactive durations

**Example Timeline:**
Business Hours: 9 AM - 6 PM (540 min total)
Polls: 10 AM (active), 2 PM (inactive), 4 PM (active)
Timeline:
- 9:00-10:00 AM: active (assumed) = 60 min uptime
- 10:00-2:00 PM: active (observed) = 240 min uptime  
- 2:00-4:00 PM: inactive (observed) = 120 min downtime
- 4:00-6:00 PM: active (observed) = 120 min uptime

Total: 420 min uptime, 120 min downtime

*why this logic works:* 
- Forward-fill status between observations
- Default to 'active' if no prior data
- Only count business hours

### Key Features
- **Database Storage**: SQLite implementation (no precomputation)
- **Missing Data Handling**: Defaults to 24/7 operation and America/Chicago timezone
- **Multi-period Calculation**: Last hour (minutes), last day/week (hours)
- **API Endpoints**: trigger_report() and get_report() with proper status tracking 


## Sample Output
Generated CSV contains uptime/downtime statistics for all stores across three time periods, enabling restaurant owners to identify patterns and operational issues.

## Future Improvements
- Implement actual REST APIs using Flask/FastAPI
- Add data validation and error handling layers  
- Implement caching for repeated calculations
- Extend to support multiple business hour intervals per day
