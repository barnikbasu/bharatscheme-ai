# BharatScheme AI - MVP Requirements

## Project Overview
An AI-powered civic information assistant that helps Indian citizens discover government schemes they're eligible for through natural language interaction.

## Functional Requirements

### Core Features
1. **Natural Language Interface**
   - Chat-based interaction for scheme discovery
   - Support for English and basic Hindi phrases
   - Simple question-answer flow for eligibility assessment

2. **User Profile Collection**
   - Age range selection
   - Income bracket (Below Poverty Line, Low Income, Middle Income, High Income)
   - Occupation category (Farmer, Student, Senior Citizen, Woman, Entrepreneur, etc.)
   - State/Union Territory selection
   - Social category (General, SC/ST, OBC, Minority)

3. **Scheme Matching & Recommendations**
   - AI-powered eligibility assessment
   - Personalized scheme recommendations
   - Explanation of why schemes match user profile
   - Priority ranking based on relevance

4. **Scheme Information Display**
   - Scheme name and brief description
   - Eligibility criteria in simple language
   - Key benefits and financial assistance details
   - Required documents list
   - Step-by-step application process
   - Official website links

### MVP Scope Limitations
- **Data Source**: Static JSON file with 15-20 sample schemes
- **No Real-time APIs**: No integration with government databases
- **Basic UI**: Simple, functional interface without advanced styling
- **Limited Schemes**: Focus on popular central government schemes
- **No User Accounts**: Stateless interaction per session

## Technical Requirements

### Frontend
- **Framework**: React.js with TypeScript
- **UI Library**: Basic CSS with responsive design
- **Accessibility**: WCAG 2.1 AA compliance basics
- **Mobile-first**: Responsive design for mobile devices
- **Browser Support**: Modern browsers (Chrome, Firefox, Safari, Edge)

### Backend
- **Runtime**: Node.js 18.x
- **Framework**: AWS Lambda with API Gateway
- **AI/ML**: Amazon Bedrock (Claude 3 Haiku for cost efficiency)
- **Data Storage**: Static JSON files in S3
- **Authentication**: None for MVP

### Infrastructure
- **Cloud Provider**: AWS
- **Deployment**: AWS CDK or SAM for infrastructure as code
- **API**: REST API via API Gateway
- **Static Hosting**: S3 + CloudFront for frontend
- **Monitoring**: Basic CloudWatch logging

## Non-Functional Requirements

### Performance
- API response time < 3 seconds
- Frontend load time < 2 seconds
- Support for 100 concurrent users

### Security
- HTTPS only
- Input validation and sanitization
- No PII storage beyond session
- Basic rate limiting

### Accessibility
- Screen reader compatible
- Keyboard navigation support
- High contrast mode support
- Simple language (Grade 8 reading level)
- Voice input capability (browser native)

### Reliability
- 99% uptime during demo period
- Graceful error handling
- Fallback responses when AI is unavailable

## User Stories

### Primary User: Rural Citizen with Low Digital Literacy
- "As a farmer, I want to ask in simple language what schemes are available for me"
- "As a senior citizen, I want to understand what benefits I can get from the government"
- "As a woman entrepreneur, I want to find schemes that support my business"

### Secondary User: Urban Educated Citizen
- "As a student, I want to quickly find scholarship and education schemes"
- "As a young professional, I want to discover housing and employment schemes"

## Success Metrics
- User completes eligibility assessment (>80%)
- User finds at least one relevant scheme (>70%)
- User understands scheme requirements (measured by follow-up questions)
- Session duration indicates engagement (2-5 minutes average)

## Responsible AI Requirements

### Transparency
- Clear disclaimer about informational nature
- Explanation
