# sunshine_app

A new Flutter project.

## Getting Started

This project is a starting point for a Flutter application.

A few resources to get you started if this is your first Flutter project:

- [Lab: Write your first Flutter app](https://docs.flutter.dev/get-started/codelab)
- [Cookbook: Useful Flutter samples](https://docs.flutter.dev/cookbook)

For help getting started with Flutter development, view the
[online documentation](https://docs.flutter.dev/), which offers tutorials,
samples, guidance on mobile development, and a full API reference.

# Sunshine Krishi App

## Introduction

The sunshine-krishi-app is a user-friendly application designed to visualize and provide insights into sunshine data. It allows users to explore sunshine hours for different timeframes (hourly, daily, weekly, monthly, yearly) and a selected date, presenting the information through interactive charts and helpful statistics, all while featuring an animated sky background that changes with the time.

## Architecture Overview

The application follows a modular architecture with eight core components that work together to provide a seamless user experience:

- **SunshineData**: The standard data model for sunshine information
- **ApiService**: Network communication layer for fetching data
- **SunshineProvider**: Central state management and data orchestration
- **ControlsWidget**: User interface for selecting dates and view modes
- **VisualizationWidget**: Interactive chart rendering component
- **InsightsWidget**: Statistical analysis and summary display
- **AnimatedSkyBackground**: Dynamic visual background system

## Module Documentation

### 1. SunshineData - The Data Blueprint

**Purpose**: SunshineData serves as the standardized format for all sunshine measurements in the application, ensuring consistent data handling across all components.

**Key Properties**:
- `timestamp`: Precise recording time (e.g., "2023-10-27T10:30:00Z")
- `sunshineHours`: Actual sunshine hours measurement (e.g., 5.5 hours)
- `date`: Calendar date for quick reference (e.g., "2023-10-27")

**Implementation**:
```dart
class SunshineData {
  final String timestamp;
  final double sunshineHours;
  final String date;
  
  SunshineData({
    required this.timestamp,
    required this.sunshineHours,
    required this.date,
  });
  
  factory SunshineData.fromJson(Map<String, dynamic> json) {
    return SunshineData(
      timestamp: json['timestamp'] as String,
      sunshineHours: (json['sunshine_hours'] as num).toDouble(),
      date: json['date'] as String,
    );
  }
}
```

### 2. ApiService - The Network Layer

**Purpose**: ApiService manages all communication with the backend server, handling data requests, response processing, and error management.

**Key Features**:
- HTTP request management using the `http` package
- JSON response parsing and conversion to SunshineData objects
- Error handling and exception reporting
- Multiple data fetching methods for different time periods

**Base Configuration**:
- Backend URL: `https://sunshine-backend-0qhd.onrender.com`

**Available Methods**:
- `fetchHourlyData(String date)`: Retrieves hourly sunshine data for a specific date
- `fetchDailyData(String month)`: Gets daily data for a month
- `fetchWeeklyData(String year, String month)`: Fetches weekly summaries
- `fetchMonthlyData()`: Provides monthly aggregations
- `fetchYearlyData()`: Returns yearly statistics
- `fetchHistoricalData()`: Accesses historical averages

### 3. SunshineProvider - The Central Brain

**Purpose**: SunshineProvider acts as the central state management system, coordinating data flow between the UI and backend services while maintaining application state.

**Core Responsibilities**:
- **Data Management**: Maintains current sunshine data collection
- **State Tracking**: Monitors loading states and error conditions
- **View Management**: Tracks current view mode and selected dates
- **Data Orchestration**: Coordinates API requests based on user selections
- **Change Notification**: Updates all listening UI components when data changes

**Key State Variables**:
- `data`: List of current SunshineData objects
- `isLoading`: Loading state indicator
- `error`: Error message storage
- `viewMode`: Current display mode ('Hourly', 'Daily', etc.)
- `selectedDate`: User-selected date
- `selectedHour`: Currently selected hour for hourly views

**Primary Methods**:
- `fetchData()`: Orchestrates data retrieval based on current settings
- `changeViewMode(String mode)`: Updates view mode and triggers data refresh
- `changeDate(DateTime date)`: Changes selected date and fetches corresponding data

### 4. ControlsWidget - The User Interface

**Purpose**: ControlsWidget provides the primary user interaction interface, allowing users to customize their data view through intuitive controls.

**Interactive Elements**:
- **View Mode Dropdown**: Selector for timeframe options (Hourly, Daily, Weekly, Monthly, Yearly)
- **Date Picker**: Calendar interface for date selection

**User Interaction Flow**:
1. User selects desired view mode from dropdown
2. ControlsWidget calls `provider.changeViewMode(newMode)`
3. User picks specific date from calendar
4. ControlsWidget calls `provider.changeDate(selectedDate)`
5. SunshineProvider automatically fetches and updates data

### 5. VisualizationWidget - The Chart Engine

**Purpose**: VisualizationWidget transforms raw sunshine data into interactive visual representations, supporting both bar and line chart formats.

**Features**:
- **Dual Chart Types**: Toggle between bar charts and line graphs
- **Interactive Elements**: Clickable bars for hour selection in hourly view
- **Loading States**: Shimmer animations during data loading
- **Error Handling**: User-friendly error message display
- **Responsive Design**: Adapts to different screen sizes

**Chart Library**: Uses `fl_chart` package for rendering

**Interaction Capabilities**:
- Hour selection in hourly mode updates `provider.selectedHour`
- Selected hours are highlighted with different colors
- Touch callbacks provide immediate visual feedback

### 6. InsightsWidget - The Data Analyst

**Purpose**: InsightsWidget processes sunshine data to generate meaningful statistics and summaries, providing quick insights without requiring chart interpretation.

**Generated Insights**:
- **Average Sunshine**: Calculates mean sunshine hours for the current dataset
- **Peak Sunshine**: Identifies maximum sunshine hours and when they occurred
- **Historical Average**: Provides long-term context through historical comparisons

**Data Processing**:
- Real-time calculations based on current SunshineProvider data
- Asynchronous loading of historical data using FutureBuilder
- Formatted display with appropriate precision (2 decimal places)

### 7. AnimatedSkyBackground - The Visual Experience

**Purpose**: AnimatedSkyBackground creates an immersive, time-sensitive visual backdrop that enhances the application's aesthetic appeal and provides intuitive time context.

**Dynamic Elements**:
- **Time-Based Colors**: Sky gradient changes based on selected or current hour
- **Celestial Objects**: Sun appears during day hours, moon during night
- **Weather Elements**: Clouds drift during daytime, stars twinkle at night
- **Smooth Animations**: Continuous movement creates a living environment

**Time Periods and Visual States**:
- **Night (0-5 AM)**: Dark sky with stars and moon
- **Sunrise (5-9 AM)**: Warm gradient colors
- **Midday (9-3 PM)**: Bright blue sky with sun and clouds
- **Sunset (3-6 PM)**: Orange and pink hues
- **Evening (6-12 PM)**: Transition back to night colors

**Technical Implementation**:
- Uses `CustomPainter` for direct canvas drawing
- `AnimationController` manages continuous 60-second animation cycles
- Responds to `selectedHour` changes from SunshineProvider
- Optimized rendering with `shouldRepaint` conditions

## Data Flow Architecture

The application follows a unidirectional data flow pattern:

```
User Interaction → ControlsWidget → SunshineProvider → ApiService → Backend Server
                                         ↓
AnimatedSkyBackground ← InsightsWidget ← VisualizationWidget ← SunshineProvider
```

1. **User Input**: Users interact with ControlsWidget to select dates and view modes
2. **State Update**: ControlsWidget updates SunshineProvider state
3. **Data Fetching**: SunshineProvider requests appropriate data from ApiService
4. **API Communication**: ApiService communicates with backend server
5. **Data Processing**: Raw JSON responses are converted to SunshineData objects
6. **UI Updates**: All listening widgets (Visualization, Insights, AnimatedSky) update automatically

## Getting Started

### Prerequisites
- Flutter SDK
- Dart programming language
- Internet connection for data fetching

### Dependencies
```yaml
dependencies:
  flutter:
    sdk: flutter
  http: ^latest_version
  provider: ^latest_version
  fl_chart: ^latest_version
  intl: ^latest_version
```

### Installation
1. Clone the repository
2. Run `flutter pub get` to install dependencies
3. Ensure backend server connectivity
4. Run `flutter run` to start the application

## Usage Examples

### Viewing Daily Data for a Specific Date
1. Open the app
2. Select "Daily" from the view mode dropdown
3. Tap the date picker and choose your desired date
4. View the daily sunshine chart and statistics

### Exploring Hourly Trends
1. Select "Hourly" view mode
2. Choose a specific date
3. Tap individual bars in the chart to see how the sky background changes
4. Review hourly insights in the summary panel

## Contributing

When contributing to this project, please ensure:
- Follow the established module structure
- Maintain the SunshineData standard for all data operations
- Use the SunshineProvider for state management
- Implement proper error handling in UI components
- Test interactive features across different view modes

## License

[Add your license information here]
