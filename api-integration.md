# Congress.gov API Integration Plan

## Current Codebase Analysis (Pseudocode)

### Overall Architecture
The application is built with React and TypeScript using Chakra UI for components. It follows a modern React application structure with:

1. **Components Directory**: Contains reusable UI components (BillList, BillCard, BillSummary, etc.)
2. **Context Directory**: Contains AppContext for global state management, primarily for pinned bills
3. **Data Directory**: Contains static JSON files (bills.json, members.json, committees.json)
4. **Services Directory**: 
   - `staticDataService.ts` - Provides methods to access/filter the static data
   - `congressApi.ts` - API service set up for Congress.gov but not fully implemented
5. **Types Directory**: Contains TypeScript interfaces for Bills, Members, Committees, etc.
6. **App.tsx**: Main application component containing most UI logic and state management

### Current Data Flow
```
┌─────────────┐      ┌─────────────┐      ┌───────────────┐
│ Static JSON │ ───> │ Data Service│ ───> │ React         │
│ Files       │      │ (Transform) │      │ Components    │
└─────────────┘      └─────────────┘      └───────────────┘
```

### Current Functionality
1. **Bill Display**: Shows static bill data with properties like title, sponsor, committees, etc.
2. **Member Display**: Shows congressional members with properties like party, state, etc.
3. **Committee Display**: Shows committee information with members and jurisdiction
4. **Map Visualization**: Uses react-simple-maps to show a US map with state-based data
5. **Search & Filtering**: Allows filtering of bills and members
6. **Pinning Bills**: Allows users to pin bills for quick access

### API Configuration
A basic API client (`congressApi.ts`) exists but isn't fully integrated. It's configured to:
- Use Axios for HTTP requests
- Add API key as a parameter
- Use a CORS proxy (cors-anywhere.herokuapp.com)
- Make basic calls to Congress.gov API endpoints for bills, members, and committees

## API Integration Plan

### Phase 1: API Authentication & Basic Connectivity

1. **API Key Management**
   - Create a `.env` file for storing the API key
   - Update the existing congressApi.ts service to use the environment variable
   - Implement basic error handling for authentication failures

2. **CORS Handling**
   - Evaluate if the current CORS proxy is reliable (cors-anywhere.herokuapp.com)
   - Consider implementing a server-side proxy if needed
   - Add fallback logic when API calls fail due to CORS issues

3. **API Client Enhancement**
   - Add request/response interceptors for consistent error handling
   - Implement retry logic for failed requests
   - Add proper TypeScript types for API responses

### Phase 2: Bill Data Integration

1. **Bill Fetching Service**
   - Enhance the `congressApi.ts` to fully implement:
     - `/bill` endpoint for recent bills
     - `/bill/{congress}/{billType}/{billNumber}` for individual bill details
     - `/bill/{congress}/{billType}/{billNumber}/subjects` for bill subjects
     - `/bill/{congress}/{billType}/{billNumber}/text` for bill text versions
     - `/bill/{congress}/{billType}/{billNumber}/actions` for bill timeline

2. **Bill Data Transformation**
   - Enhance the `convertApiBill` function to properly map API responses to our Bill interface
   - Handle edge cases where API data may be incomplete or structured differently

3. **Bill Text Integration**
   - Add functionality to retrieve and display full bill text
   - Implement a viewer for the bill text (HTML/PDF formats)
   - Add text search capabilities within bills

4. **UI Updates for Bill Components**
   - Update BillCard, BillList, and BillSummary components to work with live data
   - Add loading states and error handling to UI components
   - Implement pagination for bill lists

### Phase 3: Member & Committee Integration

1. **Member API Integration**
   - Implement `/member` endpoint for browsing members
   - Implement `/member/{bioguideId}` for individual member details
   - Add data transformation for API response to Member interface

2. **Committee API Integration**
   - Implement `/committee` endpoints to fetch committee data
   - Add data transformation for API response to Committee interface
   - Add functionality to show bills associated with committees

3. **Voting Record Integration**
   - Fetch and display member voting records on bills
   - Implement visualization for voting patterns

### Phase 4: Search & Advanced Features

1. **Search Integration**
   - Implement bill search using API parameters
   - Add filtering capabilities (by date, committee, sponsor, etc.)
   - Create an advanced search UI

2. **OpenAI Integration for Bill Analysis**
   - Implement a service to send bill text to OpenAI API
   - Create prompts for different analysis types:
     - Bill summary
     - Impact analysis
     - Comparison with other bills
     - Legal implications

3. **Caching Strategy**
   - Implement local caching for API responses
   - Add cache invalidation logic for timely updates
   - Consider using a library like SWR or react-query

### Phase 5: Error Handling & Fallback Strategy

1. **Comprehensive Error Handling**
   - Implement error boundaries in React components
   - Create user-friendly error messages
   - Add analytics for error tracking

2. **Fallback to Static Data**
   - Implement fallback logic to use static data when API fails
   - Create a pipeline for periodically updating static data

3. **Network Status Detection**
   - Add online/offline detection
   - Implement offline mode using cached data

## Technical Implementation Details

### API Service Enhancements

```typescript
// Enhanced API Service
import axios, { AxiosInstance, AxiosRequestConfig, AxiosResponse } from 'axios';

// API configuration
const API_KEY = process.env.REACT_APP_CONGRESS_API_KEY;
const BASE_URL = 'https://api.congress.gov/v3';

// Create a custom axios instance
const api: AxiosInstance = axios.create({
  baseURL: BASE_URL,
  params: {
    api_key: API_KEY,
    format: 'json'
  }
});

// Request interceptor for API calls
api.interceptors.request.use(
  (config) => {
    // You can add headers or do other transformations here
    return config;
  },
  (error) => {
    return Promise.reject(error);
  }
);

// Response interceptor for API calls
api.interceptors.response.use(
  (response) => {
    return response;
  },
  async (error) => {
    const originalRequest = error.config;
    
    // Handle specific error codes
    if (error.response?.status === 401) {
      // Handle authentication issues
      console.error('API Key is invalid or expired');
    } else if (error.response?.status === 429) {
      // Handle rate limiting
      console.error('Rate limit exceeded');
    } else if (error.response?.status === 404) {
      // Handle not found
      console.error('Resource not found');
    }
    
    return Promise.reject(error);
  }
);

// Bill endpoints
const billEndpoints = {
  // General bill endpoints
  getAllBills: (params?: any) => 
    api.get('/bill', { params }),
    
  getBillsByCongress: (congress: number, params?: any) => 
    api.get(`/bill/${congress}`, { params }),
    
  getBillsByTypeAndCongress: (congress: number, billType: string, params?: any) => 
    api.get(`/bill/${congress}/${billType}`, { params }),
    
  getBillById: (congress: number, billType: string, billNumber: string) => 
    api.get(`/bill/${congress}/${billType}/${billNumber}`),
  
  // Bill details endpoints
  getBillActions: (congress: number, billType: string, billNumber: string, params?: any) => 
    api.get(`/bill/${congress}/${billType}/${billNumber}/actions`, { params }),
    
  getBillAmendments: (congress: number, billType: string, billNumber: string, params?: any) => 
    api.get(`/bill/${congress}/${billType}/${billNumber}/amendments`, { params }),
    
  getBillCommittees: (congress: number, billType: string, billNumber: string, params?: any) => 
    api.get(`/bill/${congress}/${billType}/${billNumber}/committees`, { params }),
    
  getBillCosponsors: (congress: number, billType: string, billNumber: string, params?: any) => 
    api.get(`/bill/${congress}/${billType}/${billNumber}/cosponsors`, { params }),
    
  getBillRelatedBills: (congress: number, billType: string, billNumber: string, params?: any) => 
    api.get(`/bill/${congress}/${billType}/${billNumber}/relatedbills`, { params }),
    
  getBillSubjects: (congress: number, billType: string, billNumber: string, params?: any) => 
    api.get(`/bill/${congress}/${billType}/${billNumber}/subjects`, { params }),
    
  getBillSummaries: (congress: number, billType: string, billNumber: string, params?: any) => 
    api.get(`/bill/${congress}/${billType}/${billNumber}/summaries`, { params }),
    
  getBillText: (congress: number, billType: string, billNumber: string, params?: any) => 
    api.get(`/bill/${congress}/${billType}/${billNumber}/text`, { params }),
    
  getBillTitles: (congress: number, billType: string, billNumber: string, params?: any) => 
    api.get(`/bill/${congress}/${billType}/${billNumber}/titles`, { params })
};

// Summaries endpoints
const summaryEndpoints = {
  getAllSummaries: (params?: any) => 
    api.get('/summaries', { params }),
    
  getSummariesByCongress: (congress: number, params?: any) => 
    api.get(`/summaries/${congress}`, { params }),
    
  getSummariesByCongressAndBillType: (congress: number, billType: string, params?: any) => 
    api.get(`/summaries/${congress}/${billType}`, { params })
};

// Congress endpoints
const congressEndpoints = {
  getAllCongresses: (params?: any) => 
    api.get('/congress', { params }),
    
  getCongressById: (congress: number, params?: any) => 
    api.get(`/congress/${congress}`, { params }),
    
  getCurrentCongress: (params?: any) => 
    api.get('/congress/current', { params })
};

// Member endpoints
const memberEndpoints = {
  getAllMembers: (params?: any) => 
    api.get('/member', { params }),
    
  getMemberById: (bioguideId: string, params?: any) => 
    api.get(`/member/${bioguideId}`, { params }),
    
  getMemberSponsoredLegislation: (bioguideId: string, params?: any) => 
    api.get(`/member/${bioguideId}/sponsored-legislation`, { params }),
    
  getMemberCosponsoredLegislation: (bioguideId: string, params?: any) => 
    api.get(`/member/${bioguideId}/cosponsored-legislation`, { params }),
    
  getMembersByCongress: (congress: number, params?: any) => 
    api.get(`/member/congress/${congress}`, { params }),
    
  getMembersByState: (stateCode: string, params?: any) => 
    api.get(`/member/${stateCode}`, { params }),
    
  getMembersByStateAndDistrict: (stateCode: string, district: string, params?: any) => 
    api.get(`/member/${stateCode}/${district}`, { params }),
    
  getMembersByCongressStateAndDistrict: (congress: number, stateCode: string, district: string, params?: any) => 
    api.get(`/member/congress/${congress}/${stateCode}/${district}`, { params })
};

// Committee endpoints
const committeeEndpoints = {
  getAllCommittees: (params?: any) => 
    api.get('/committee', { params }),
    
  getCommitteesByChamber: (chamber: 'house' | 'senate' | 'joint', params?: any) => 
    api.get(`/committee/${chamber}`, { params }),
    
  getCommitteesByCongress: (congress: number, params?: any) => 
    api.get(`/committee/${congress}`, { params }),
    
  getCommitteesByCongressAndChamber: (congress: number, chamber: 'house' | 'senate' | 'joint', params?: any) => 
    api.get(`/committee/${congress}/${chamber}`, { params }),
    
  getCommitteeDetails: (chamber: 'house' | 'senate', committeeCode: string, params?: any) => 
    api.get(`/committee/${chamber}/${committeeCode}`, { params }),
    
  getCommitteeBills: (chamber: 'house' | 'senate', committeeCode: string, params?: any) => 
    api.get(`/committee/${chamber}/${committeeCode}/bills`, { params }),
    
  getCommitteeReports: (chamber: 'house' | 'senate', committeeCode: string, params?: any) => 
    api.get(`/committee/${chamber}/${committeeCode}/reports`, { params }),
    
  getCommitteeNominations: (chamber: 'house' | 'senate', committeeCode: string, params?: any) => 
    api.get(`/committee/${chamber}/${committeeCode}/nominations`, { params }),
    
  getCommitteeHouseCommunications: (chamber: 'house' | 'senate', committeeCode: string, params?: any) => 
    api.get(`/committee/${chamber}/${committeeCode}/house-communication`, { params }),
    
  getCommitteeSenateCommunitications: (chamber: 'house' | 'senate', committeeCode: string, params?: any) => 
    api.get(`/committee/${chamber}/${committeeCode}/senate-communication`, { params })
};

// Committee Report endpoints
const committeeReportEndpoints = {
  getAllCommitteeReports: (params?: any) => 
    api.get('/committee-report', { params }),
    
  getCommitteeReportsByCongress: (congress: number, params?: any) => 
    api.get(`/committee-report/${congress}`, { params }),
    
  getCommitteeReportsByCongressAndType: (congress: number, reportType: string, params?: any) => 
    api.get(`/committee-report/${congress}/${reportType}`, { params }),
    
  getCommitteeReportDetails: (congress: number, reportType: string, reportNumber: string, params?: any) => 
    api.get(`/committee-report/${congress}/${reportType}/${reportNumber}`, { params }),
    
  getCommitteeReportText: (congress: number, reportType: string, reportNumber: string, params?: any) => 
    api.get(`/committee-report/${congress}/${reportType}/${reportNumber}/text`, { params })
};

// Committee Print endpoints
const committeePrintEndpoints = {
  getAllCommitteePrints: (params?: any) => 
    api.get('/committee-print', { params }),
    
  getCommitteePrintsByCongress: (congress: number, params?: any) => 
    api.get(`/committee-print/${congress}`, { params }),
    
  getCommitteePrintsByCongressAndChamber: (congress: number, chamber: 'house' | 'senate', params?: any) => 
    api.get(`/committee-print/${congress}/${chamber}`, { params }),
    
  getCommitteePrintDetails: (congress: number, chamber: 'house' | 'senate', jacketNumber: string, params?: any) => 
    api.get(`/committee-print/${congress}/${chamber}/${jacketNumber}`, { params }),
    
  getCommitteePrintText: (congress: number, chamber: 'house' | 'senate', jacketNumber: string, params?: any) => 
    api.get(`/committee-print/${congress}/${chamber}/${jacketNumber}/text`, { params })
};

// Committee Meeting endpoints
const committeeMeetingEndpoints = {
  getAllCommitteeMeetings: (params?: any) => 
    api.get('/committee-meeting', { params }),
    
  getCommitteeMeetingsByCongress: (congress: number, params?: any) => 
    api.get(`/committee-meeting/${congress}`, { params }),
    
  getCommitteeMeetingsByCongressAndChamber: (congress: number, chamber: 'house' | 'senate', params?: any) => 
    api.get(`/committee-meeting/${congress}/${chamber}`, { params }),
    
  getCommitteeMeetingDetails: (congress: number, chamber: 'house' | 'senate', eventId: string, params?: any) => 
    api.get(`/committee-meeting/${congress}/${chamber}/${eventId}`, { params })
};

// Hearing endpoints
const hearingEndpoints = {
  getAllHearings: (params?: any) => 
    api.get('/hearing', { params }),
    
  getHearingsByCongress: (congress: number, params?: any) => 
    api.get(`/hearing/${congress}`, { params }),
    
  getHearingsByCongressAndChamber: (congress: number, chamber: 'house' | 'senate', params?: any) => 
    api.get(`/hearing/${congress}/${chamber}`, { params }),
    
  getHearingDetails: (congress: number, chamber: 'house' | 'senate', jacketNumber: string, params?: any) => 
    api.get(`/hearing/${congress}/${chamber}/${jacketNumber}`, { params })
};

// Congressional Record endpoints
const congressionalRecordEndpoints = {
  getAllCongressionalRecords: (params?: any) => 
    api.get('/congressional-record', { params })
};

// Daily Congressional Record endpoints
const dailyCongressionalRecordEndpoints = {
  getAllDailyCongressionalRecords: (params?: any) => 
    api.get('/daily-congressional-record', { params }),
    
  getDailyCongressionalRecordsByVolume: (volumeNumber: number, params?: any) => 
    api.get(`/daily-congressional-record/${volumeNumber}`, { params }),
    
  getDailyCongressionalRecordsByVolumeAndIssue: (volumeNumber: number, issueNumber: number, params?: any) => 
    api.get(`/daily-congressional-record/${volumeNumber}/${issueNumber}`, { params }),
    
  getDailyCongressionalRecordArticles: (volumeNumber: number, issueNumber: number, params?: any) => 
    api.get(`/daily-congressional-record/${volumeNumber}/${issueNumber}/articles`, { params })
};

// Export all endpoints
export const congressApi = {
  ...billEndpoints,
  ...summaryEndpoints,
  ...congressEndpoints,
  ...memberEndpoints,
  ...committeeEndpoints,
  ...committeeReportEndpoints,
  ...committeePrintEndpoints,
  ...committeeMeetingEndpoints,
  ...hearingEndpoints,
  ...congressionalRecordEndpoints,
  ...dailyCongressionalRecordEndpoints
};
```

### Data Transformation Functions

```typescript
// Enhanced Bill Transformation
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
    votes: transformVotes(apiBill.votes),
    timeline,
    status: {
      current: apiBill.latestAction?.text || '',
      stage: determineStage(apiBill.latestAction?.text || ''),
      isActive: isActiveBill(apiBill.latestAction?.text || ''),
      lastUpdated: apiBill.updateDate || apiBill.latestAction?.actionDate || ''
    }
  };
}

// Helper functions for data transformation
function transformSponsor(sponsor: any) {
  if (!sponsor) return null;
  
  return {
    bioguideId: sponsor.bioguideId || '',
    district: sponsor.district || 0,
    firstName: sponsor.firstName || '',
    fullName: sponsor.fullName || '',
    isByRequest: sponsor.isByRequest || 'N',
    lastName: sponsor.lastName || '',
    party: sponsor.party || '',
    state: sponsor.state || '',
    url: sponsor.url || ''
  };
}

function transformCommittees(committeeData: any) {
  if (!committeeData) {
    return { count: 0, items: [] };
  }
  
  return {
    count: committeeData.count || 0,
    items: Array.isArray(committeeData.committees) ? committeeData.committees.map((committee: any) => ({
      activities: committee.activities || [],
      chamber: committee.chamber || '',
      name: committee.name || '',
      systemCode: committee.systemCode || '',
      type: committee.type || '',
      url: committee.url || ''
    })) : []
  };
}
```

### OpenAI Integration for Bill Analysis

```typescript
// OpenAI API Integration for Bill Analysis
import axios from 'axios';

const OPENAI_API_KEY = process.env.REACT_APP_OPENAI_API_KEY;
const OPENAI_API_URL = 'https://api.openai.com/v1/chat/completions';

type AnalysisType = 'summary' | 'impact' | 'comparison' | 'legal';

interface AnalysisOptions {
  type: AnalysisType;
  billText: string;
  additionalContext?: string;
  comparisonBillText?: string;
}

export async function analyzeBill({ type, billText, additionalContext = '', comparisonBillText = '' }: AnalysisOptions) {
  // Truncate bill text if too long
  const truncatedText = billText.length > 15000 ? billText.substring(0, 15000) + '...' : billText;
  
  // Build prompt based on analysis type
  let prompt = '';
  switch (type) {
    case 'summary':
      prompt = `Provide a clear, concise summary of this legislative bill in plain language that explains its key provisions and purpose. Highlight the most important aspects that would affect citizens:\n\n${truncatedText}`;
      break;
    case 'impact':
      prompt = `Analyze the potential impacts of this legislative bill. Explain who would be affected, what changes would occur, and outline potential benefits and drawbacks:\n\n${truncatedText}`;
      break;
    case 'comparison':
      prompt = `Compare and contrast these two legislative bills. Identify similarities, differences, and explain how they relate to each other:\n\nBILL 1:\n${truncatedText}\n\nBILL 2:\n${comparisonBillText}`;
      break;
    case 'legal':
      prompt = `Provide a legal analysis of this legislative bill. Identify key legal concepts, potential constitutional issues, and how it might interact with existing laws:\n\n${truncatedText}`;
      break;
  }
  
  // Add additional context if provided
  if (additionalContext) {
    prompt += `\n\nAdditional context: ${additionalContext}`;
  }
  
  try {
    const response = await axios.post(
      OPENAI_API_URL,
      {
        model: 'gpt-4',
        messages: [
          { role: 'system', content: 'You are a legislative expert who provides objective, clear analysis of bills and legislation.' },
          { role: 'user', content: prompt }
        ],
        temperature: 0.3,
        max_tokens: 1500
      },
      {
        headers: {
          'Content-Type': 'application/json',
          'Authorization': `Bearer ${OPENAI_API_KEY}`
        }
      }
    );
    
    return response.data.choices[0].message.content;
  } catch (error) {
    console.error('Error analyzing bill with OpenAI:', error);
    throw new Error('Failed to analyze bill text. Please try again later.');
  }
}
```

## Timeline for Implementation

1. **Week 1**: Set up API authentication and establish basic connectivity
2. **Week 2-3**: Implement bill data integration and update UI components
3. **Week 4**: Implement member and committee data integration
4. **Week 5**: Add search and filtering capabilities
5. **Week 6**: Integrate with OpenAI for bill analysis
6. **Week 7**: Implement caching and error handling/fallback strategies
7. **Week 8**: Testing, optimization, and final adjustments

## Conclusion

This integration plan will transform our application from using static data to dynamically fetching real-time legislative information from Congress.gov. The phased approach allows for incremental implementation while maintaining a working application at each step. By the end of this process, we'll have a fully functional Legislative Lens application capable of providing comprehensive, up-to-date information on bills, members, and committees with intelligent bill analysis capabilities through OpenAI.
