# Legislative Lens - Codebase Overview

Legislative Lens is a React application designed to provide insights into legislative data from Congress.gov. The application allows users to explore bills, members of Congress, committees, and voting patterns.

## Project Structure

The project follows a standard React application structure with TypeScript:

```tsx
src/
├── components/     # UI components organized by domain
├── context/        # React context for global state
├── data/           # Static JSON data files
├── services/       # API and data services
├── types/          # TypeScript type definitions
├── utils/          # Utility functions
├── App.tsx         # Main application component
├── index.tsx       # Application entry point
```

## Core Technologies

- **React**: UI library
- **TypeScript**: Type safety
- **Chakra UI**: Component library for styling
- **React Simple Maps**: For US state map visualization
- **D3-scale**: For data visualization scaling

## Key Features

### 1. Main Application

```tsx
App.tsx
```

The main application component integrates all the features. It:
- Manages the application state (bills, members, committees)
- Handles routing and navigation
- Contains the responsive layout with adaptive UI for mobile/desktop
- Implements search functionality

### 2. Data Management

```tsx
services/staticDataService.ts
```

This service acts as a data repository that:
- Provides access to static mock data
- Transforms raw data into application-specific formats
- Contains methods to filter data by various criteria

```tsx
types/bill.ts
types/member.ts
types/committee.ts
types/common.ts
```

These files define the TypeScript interfaces for the core data models.

### 3. Bills Module

Bills are the core legislative documents tracked by the application.

```tsx
components/bill/BillList.tsx
components/bill/BillCard.tsx
components/bill/BillSummary.tsx
```

Key bill features:
- Bill searching and filtering
- Bill status tracking
- Timeline visualization of bill progress
- Committee referral information
- Voting record display

### 4. Members Module

Members represent senators and representatives in Congress.

```tsx
components/member/MemberCard.tsx
components/member/StateMembersModal.tsx
```

Key member features:
- Search and filter by state, party, chamber
- Member profile with photo and biographical information
- Committee assignments
- Voting record visualization
- State delegation view

### 5. Committees Module

Committees are the working groups in Congress that handle legislation.

```tsx
components/committee/CommitteeModal.tsx
```

Key committee features:
- Committee details including membership
- Recent activity tracking
- Meeting schedules
- Jurisdiction information
- Subcommittee organization

### 6. Interactive Map

```tsx
App.tsx (EnhancedMap component)
```

The US map visualization:
- Enables state selection
- Shows voting patterns by state
- Links to state delegation information

### 7. State Management

```tsx
context/AppContext.tsx
```

Context API manages global application state:
- Pinned bills for quick access
- Selected member and bill details
- Search parameters

## Data Flow

1. Static data is loaded through the `staticDataService`
2. Components request data from the service
3. Results are rendered in the UI
4. User interactions (search, filters, selections) update the application state
5. UI components re-render based on state changes

## UI Components

The application uses Chakra UI for consistent styling:
- Responsive layout with grid and flex components
- Modals for detailed information display
- Cards for summarized information
- Tabs for organized content sections
- Searchable dropdowns and inputs

## Future Enhancements

Based on the MVP plan, future enhancements include:
- Integration with live Congress.gov API (currently using static data)
- Enhanced member profiles with voting history
- Improved timeline visualization
- Additional data visualizations for voting patterns
- Mobile optimization improvements
- User authentication and saved preferences 