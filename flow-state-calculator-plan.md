# Plan for Flow State Calculation Script


## 1. Requirements & Setup
- [x] Add README with installation instructions
- [ ] ~~Use the Google Calendar API to access a specific user's calendar.~~
- [x] Use an exported .ics file as the calendar data source.
- [x] Use a configuration file (YAML, JSON, or .env) to specify:
  - Working days (e.g., Mon-Fri)
  - Working hours (e.g., 10:00–19:00)
  - Recovery period after meetings (e.g., 30 minutes)
  - **Analysis period can be set via --start-date and --end-date arguments (default: one month from today)**
  - **Excluded meeting patterns (meetings with these titles won't count as interruptions)**

## 2. Calendar Data Import
- [x] Parse the .ics file to extract events.
- [x] Filter events to only those within the configured working days and hours, and within the specified analysis period.
- [x] Exclude events with titles matching the excluded meeting patterns in config.

## 3. Time Slot Analysis
- [x] For each day:
  - Initialize the available time as the full working hours.
  - For each meeting:
    - Exclude the entire meeting duration from creative work time.
    - If the meeting starts within working hours, also exclude 5 minutes before the meeting.
    - After the meeting, apply the recovery period (e.g., 30 minutes).
      - If the next meeting starts before the recovery period ends, the time between meetings is counted with a 0.5 coefficient (i.e., only half as productive).
      - Otherwise, the full recovery period is counted with 0.5 coefficient.
  - Sum up the total creative work time for the day, applying coefficients as needed.
  - Handle offset-naive and offset-aware datetimes to avoid comparison errors.

## 4. Output Results
- [x] For each day, calculate the total hours available for creative work (printed as text).
- [x] Generate an HTML file:
  - The HTML template is stored in a separate file (`flow_state_template.html`).
  - Each day is represented as a vertical bar (column).
  - The height of the bar is scaled using the maximum possible work hours per day from config, not the max value in results.
  - Bar color transitions from red (less hours) to green (more hours).
  - Bar value text is placed inside the bar if tall enough, otherwise above, to avoid overlap.
  - Chart is horizontally scrollable when there are too many days to fit on screen, with visual indicators (scroll indicator arrow and instructional text).
  - Optionally, add labels and tooltips for clarity.
  - Display configuration settings used to generate the report (working days, working hours, recovery period, analysis period).
- [x] Calculate and display total creative hours left for the period (absolute and percent of total work hours) in the HTML report.

## 5. Configuration & Customization
- [x] Allow the user to specify:
  - The time period to analyze (via --start-date and --end-date, or default)
  - The .ics file to use as input.
  - Debug mode (--debug) to show detailed meeting information for each day.
  - Excluded meeting patterns (meetings to ignore during analysis).

## 6. Error Handling & Logging
- [x] Handle file errors, missing configuration, and edge cases (e.g., offset-naive/aware datetimes).
- [x] Log progress and issues for debugging (via --debug flag).

## 7. Packaging
- [x] ~~Provide a requirements.txt or pyproject.toml for dependencies (e.g., icalendar, pandas, jinja2/matplotlib/plotly for HTML generation).~~
- [x] Include a README with setup and usage instructions. 
