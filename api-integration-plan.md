# Congress.gov API Integration Plan

This document outlines a detailed, step-by-step plan for integrating the Congress.gov API into our Legislative Lens application. Each task has a checkbox that can be marked as complete as we progress.

## Phase 1: API Setup & Authentication
- [ ] 1.1 Create `.env` file for API key storage
- [ ] 1.2 Add Congress.gov API key to the environment variables
- [ ] 1.3 Configure axios instance in `congressApi.ts` service to use the API key
- [ ] 1.4 Implement proper error handling for authentication failures
- [ ] 1.5 Test API connectivity with basic endpoint calls
- [ ] 1.6 Implement server-side proxy for API requests
  - [ ] 1.6.1 Create server endpoint to forward requests to Congress.gov API
  - [ ] 1.6.2 Secure API key on server side
  - [ ] 1.6.3 Add rate limiting and request caching on server

## Phase 2: Bill Data Integration
- [ ] 2.1 Enhance `congressApi.ts` service to implement bill endpoints
  - [ ] 2.1.1 `/bill` endpoint for recent bills
  - [ ] 2.1.2 `/bill/{congress}/{billType}/{billNumber}` for individual bill details
  - [ ] 2.1.3 `/bill/{congress}/{billType}/{billNumber}/subjects` for bill subjects
  - [ ] 2.1.4 `/bill/{congress}/{billType}/{billNumber}/actions` for bill timeline
  - [ ] 2.1.5 `/bill/{congress}/{billType}/{billNumber}/text` for bill text versions
- [ ] 2.2 Create data transformation functions to map API responses to our interfaces
  - [ ] 2.2.1 Implement `convertApiBill` function
  - [ ] 2.2.2 Implement transformers for sponsors, cosponsors, committees, etc.
  - [ ] 2.2.3 Add handling for timeline/actions data
- [ ] 2.3 Update React components to work with live API data
  - [ ] 2.3.1 Modify BillCard component
  - [ ] 2.3.2 Modify BillList component
  - [ ] 2.3.3 Modify BillSummary component
  - [ ] 2.3.4 Add loading states and error handling to UI components
- [ ] 2.4 Implement bill text viewing capabilities
  - [ ] 2.4.1 Create UI for displaying bill text
  - [ ] 2.4.2 Add text format selection (HTML/PDF)
  - [ ] 2.4.3 Implement search within bill text

## Phase 3: Member & Committee Integration
- [ ] 3.1 Implement member endpoints in API service
  - [ ] 3.1.1 `/member` endpoint for browsing members
  - [ ] 3.1.2 `/member/{bioguideId}` for individual member details
  - [ ] 3.1.3 `/member/{bioguideId}/sponsored-legislation` for sponsored bills
  - [ ] 3.1.4 `/member/{bioguideId}/cosponsored-legislation` for cosponsored bills
- [ ] 3.2 Implement committee endpoints in API service
  - [ ] 3.2.1 `/committee` endpoints to fetch committee data
  - [ ] 3.2.2 `/committee/{chamber}/{committeeCode}` for committee details
  - [ ] 3.2.3 `/committee/{chamber}/{committeeCode}/bills` for committee bills
- [ ] 3.3 Create data transformation functions for member and committee data
  - [ ] 3.3.1 Implement `convertApiMember` function
  - [ ] 3.3.2 Implement `convertApiCommittee` function
- [ ] 3.4 Update React components to display member and committee data
  - [ ] 3.4.1 Update MemberCard component
  - [ ] 3.4.2 Update MemberList component
  - [ ] 3.4.3 Update CommitteeCard component
  - [ ] 3.4.4 Update CommitteeList component
- [ ] 3.5 Implement member voting record visualization
  - [ ] 3.5.1 Create voting record component
  - [ ] 3.5.2 Implement visual representation of voting patterns

## Phase 4: Search & Advanced Features
- [ ] 4.1 Implement search functionality using API parameters
  - [ ] 4.1.1 Create advanced search form UI
  - [ ] 4.1.2 Implement bill search with multiple parameters
  - [ ] 4.1.3 Implement member search
  - [ ] 4.1.4 Implement committee search
- [ ] 4.2 Add filtering capabilities
  - [ ] 4.2.1 Filter by date range
  - [ ] 4.2.2 Filter by committee
  - [ ] 4.2.3 Filter by sponsor
  - [ ] 4.2.4 Filter by policy area/subject
- [ ] 4.3 Implement OpenAI integration for bill analysis
  - [ ] 4.3.1 Set up OpenAI API client with proper authentication
  - [ ] 4.3.2 Create service for sending bill text to OpenAI API
  - [ ] 4.3.3 Implement different analysis types (summary, impact, comparison, legal)
  - [ ] 4.3.4 Create UI for displaying analysis results

## Phase 5: Caching & Performance
- [ ] 5.1 Implement local caching for API responses
  - [ ] 5.1.1 Set up client-side caching with appropriate TTLs
  - [ ] 5.1.2 Implement cache invalidation logic
  - [ ] 5.1.3 Add cache headers to API requests
- [ ] 5.2 Consider implementing SWR or react-query for data fetching
  - [ ] 5.2.1 Evaluate libraries and select the most appropriate
  - [ ] 5.2.2 Refactor API calls to use the selected library
  - [ ] 5.2.3 Implement optimistic UI updates
- [ ] 5.3 Add pagination for list views
  - [ ] 5.3.1 Implement pagination controls
  - [ ] 5.3.2 Add infinite scrolling option for lists
  - [ ] 5.3.3 Optimize initial load time

## Phase 6: Error Handling & Fallback Strategy
- [ ] 6.1 Implement comprehensive error handling
  - [ ] 6.1.1 Add error boundaries in React components
  - [ ] 6.1.2 Create user-friendly error messages
  - [ ] 6.1.3 Implement logging for error tracking
- [ ] 6.2 Create fallback to static data when API fails
  - [ ] 6.2.1 Implement fallback logic in data services
  - [ ] 6.2.2 Create pipeline for periodically updating static data
  - [ ] 6.2.3 Test fallback scenarios
- [ ] 6.3 Add network status detection
  - [ ] 6.3.1 Implement online/offline detection
  - [ ] 6.3.2 Create offline mode using cached data
  - [ ] 6.3.3 Add reconnection logic

## Phase 7: Testing & Deployment
- [ ] 7.1 Write unit tests for API services
  - [ ] 7.1.1 Test data transformation functions
  - [ ] 7.1.2 Test error handling
  - [ ] 7.1.3 Test fallback mechanisms
- [ ] 7.2 Write integration tests for component-API interactions
  - [ ] 7.2.1 Test component rendering with real API data
  - [ ] 7.2.2 Test search and filtering functionality
  - [ ] 7.2.3 Test error states and loading indicators
- [ ] 7.3 Perform end-to-end testing
  - [ ] 7.3.1 Test complete user flows
  - [ ] 7.3.2 Test performance and loading times
  - [ ] 7.3.3 Test on different devices and browsers
- [ ] 7.4 Deploy to staging environment
  - [ ] 7.4.1 Set up environment variables
  - [ ] 7.4.2 Verify API connectivity in staging
  - [ ] 7.4.3 Perform QA testing
- [ ] 7.5 Deploy to production
  - [ ] 7.5.1 Set up production environment variables
  - [ ] 7.5.2 Create rollback plan
  - [ ] 7.5.3 Monitor initial deployment

## Implementation Timeline
- **Week 1**: Complete Phase 1 (API Setup & Authentication)
- **Weeks 2-3**: Complete Phase 2 (Bill Data Integration)
- **Week 4**: Complete Phase 3 (Member & Committee Integration)
- **Week 5**: Complete Phase 4 (Search & Advanced Features)
- **Week 6**: Complete Phase 5 (Caching & Performance)
- **Week 7**: Complete Phase 6 (Error Handling & Fallback Strategy)
- **Week 8**: Complete Phase 7 (Testing & Deployment)

## Technical Notes

### API Client Structure
```typescript
// Enhanced API Service
import axios, { AxiosInstance } from 'axios';

// Using server-side proxy approach instead of direct API calls
// API key is stored and used only on the server side
const BASE_URL = '/api/congress'; // Local server endpoint that proxies to Congress.gov API

// Create a custom axios instance
const api: AxiosInstance = axios.create({
  baseURL: BASE_URL,
  params: {
    format: 'json'
  }
});

// Interceptors for consistent error handling
api.interceptors.response.use(
  (response) => response,
  async (error) => {
    if (error.response?.status === 401) {
      console.error('Authentication error');
    } else if (error.response?.status === 429) {
      console.error('Rate limit exceeded');
    }
    return Promise.reject(error);
  }
);

// Export the API client with all endpoints
export const congressApi = {
  // Bill endpoints
  getAllBills: (params?: any) => 
    api.get('/bill', { params }),
  
  getBillById: (congress: number, billType: string, billNumber: string) => 
    api.get(`/bill/${congress}/${billType}/${billNumber}`),
  
  // Member endpoints
  getAllMembers: (params?: any) => 
    api.get('/member', { params }),
  
  getMemberById: (bioguideId: string) => 
    api.get(`/member/${bioguideId}`),
  
  // Committee endpoints
  getAllCommittees: (params?: any) => 
    api.get('/committee', { params }),
  
  getCommitteeDetails: (chamber: 'house' | 'senate', committeeCode: string) => 
    api.get(`/committee/${chamber}/${committeeCode}`)
};
```

### Data Transformation Example
```typescript
// Convert API bill response to our Bill interface
export function convertApiBill(apiBill: any): Bill {
  // Extract timeline events from actions if available
  const timeline = { milestones: [] };
  
  if (apiBill.actions && Array.isArray(apiBill.actions.actions)) {
    timeline.milestones = apiBill.actions.actions.map((action: any) => ({
      date: action.actionDate,
      title: action.type || 'Action',
      text: action.text,
      type: mapActionTypeToTimelineType(action.type),
      status: 'complete',
      details: {
        location: action.sourceSystem?.name,
        actionBy: action.actionBy
      }
    }));
  }
  
  return {
    congress: apiBill.congress,
    billType: apiBill.type?.toLowerCase() || '',
    billNumber: apiBill.number || '',
    title: apiBill.title || '',
    displayTitle: apiBill.titles?.[0]?.title || apiBill.title || '',
    summary: apiBill.summaries?.[0]?.text || '',
    introducedDate: apiBill.introducedDate || '',
    policyArea: apiBill.policyArea || { name: '' },
    sponsor: transformSponsor(apiBill.sponsors?.[0]),
    latestAction: apiBill.latestAction,
    committees: transformCommittees(apiBill.committees),
    textVersions: {
      count: apiBill.textVersions?.count || 0,
      url: apiBill.textVersions?.url || ''
    },
    timeline,
    status: {
      current: apiBill.latestAction?.text || '',
      stage: determineStage(apiBill.latestAction?.text || ''),
      isActive: isActiveBill(apiBill.latestAction?.text || ''),
      lastUpdated: apiBill.updateDate || apiBill.latestAction?.actionDate || ''
    }
  };
}
``` 