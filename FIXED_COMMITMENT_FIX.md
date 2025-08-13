# Fixed Commitment Scheduling Fix

## Problem
Fixed commitments (commitments with `isFixed: true`) were not properly preventing task scheduling on specific days, even when the toggle was enabled. Tasks were still being scheduled on days with fixed commitments.

## Root Cause
1. **Daily available hours calculation**: The scheduling system was not considering fixed commitments when calculating initial daily available hours
2. **Time slot detection**: The `findNextAvailableTimeSlot` function was ignoring the `isFixed` property of commitments
3. **All-day commitment handling**: All-day events were completely ignored for scheduling regardless of the `isFixed` flag

## Solution
### 1. Added `calculateDailyAvailableHours` function
- Calculates actual available hours per day considering fixed commitments
- Handles both all-day and time-specific fixed commitments
- Accounts for modified occurrences and deleted occurrences
- Returns 0 available hours for days with fixed all-day commitments

### 2. Updated scheduling initialization
- Both "even" and "balanced" distribution modes now use `calculateDailyAvailableHours`
- Fixed commitments are properly subtracted from daily available hours
- Study plans reflect the correct available hours considering fixed commitments

### 3. Enhanced `findNextAvailableTimeSlot` function
- Added early return for days with fixed all-day commitments
- Only blocks time slots for commitments with `isFixed: true`
- Non-fixed commitments no longer block study session scheduling

## Files Modified
- `src/utils/scheduling.ts`: Main scheduling logic and time slot detection
  - Added `calculateDailyAvailableHours()` function
  - Updated even distribution mode (lines 745-754)
  - Updated balanced distribution mode (lines 1593-1600) 
  - Enhanced `findNextAvailableTimeSlot()` function (lines 440-450, 471-505)

## Impact
- Fixed commitments now properly prevent task scheduling on the specified days
- All-day fixed commitments block all study sessions for the entire day
- Time-specific fixed commitments block only the specified time ranges
- Non-fixed commitments are treated as informational and don't block scheduling
- Improved scheduling accuracy and user experience
