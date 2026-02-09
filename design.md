# BharatScheme AI - MVP Design Document

## System Architecture

### High-Level Architecture
┌─────────────────┐ ┌──────────────────┐ ┌─────────────────┐ │ React Web │ │ API Gateway │ │ Lambda Function│ │ Application │◄──►│ REST API │◄──►│ Scheme Matcher │ │ │ │ │ │ │ └─────────────────┘ └──────────────────┘ └─────────────────┘ │ │ │ │ │ ▼ │ │ ┌─────────────────┐ │ │ │ Amazon Bedrock │ │ │ │ (Claude 3 Haiku)│ │ │ └─────────────────┘ │ │ │ ▼ ▼ ▼ ┌─────────────────┐ ┌──────────────────┐ ┌─────────────────┐ │ CloudFront │ │ CloudWatch │ │ S3 │ │ CDN │ │ Logs │ │ Schemes Data │ └─────────────────┘ └──────────────────┘ └─────────────────┘


### Component Design

#### 1. Frontend Application (React)

**File Structure:**
src/ ├── components/ │ ├── ChatInterface/ │ │ ├── ChatWindow.tsx │ │ ├── MessageBubble.tsx │ │ └── InputField.tsx │ ├── UserProfile/ │ │ ├── ProfileForm.tsx │ │ └── QuickQuestions.tsx │ ├── SchemeDisplay/ │ │ ├── SchemeCard.tsx │ │ ├── SchemeDetails.tsx │ │ └── EligibilityExplanation.tsx │ └── Common/ │ ├── Header.tsx │ ├── Footer.tsx │ └── Disclaimer.tsx ├── services/ │ ├── api.ts │ └── types.ts ├── hooks/ │ ├── useChat.ts │ └── useUserProfile.ts └── utils/ ├── accessibility.ts └── constants.ts


**Key Components:**

1. **ChatInterface**
   - Real-time chat UI with typing indicators
   - Message history with user/bot distinction
   - Voice input integration (browser Speech API)
   - Accessibility: ARIA labels, keyboard navigation

2. **UserProfile**
   - Progressive disclosure form
   - Visual selection for low-literacy users
   - State persistence during session
   - Validation with helpful error messages

3. **SchemeDisplay**
   - Card-based layout for schemes
   - Expandable details with step-by-step guidance
   - Visual indicators for eligibility match
   - Share/bookmark functionality

#### 2. Backend API (AWS Lambda)

**Function Structure:**
lambda/ ├── handlers/ │ ├── chatHandler.ts │ ├── schemeHandler.ts │ └── profileHandler.ts ├── services/ │ ├── bedrockService.ts │ ├── schemeMatchingService.ts │ └── eligibilityService.ts ├── models/ │ ├── UserProfile.ts │ ├── Scheme.ts │ └── ChatMessage.ts └── utils/ ├── responseFormatter.ts └── validation.ts


**API Endpoints:**

1. **POST /chat**
   ```json
   {
     "message": "I am a farmer looking for schemes",
     "userProfile": {
       "age": "35-50",
       "income": "low",
       "occupation": "farmer",
       "state": "uttar-pradesh",
       "category": "general"
     },
     "sessionId": "uuid"
   }
GET /schemes

{
  "filters": {
    "occupation": "farmer",
    "state": "uttar-pradesh"
  },
  "limit": 10
}
POST /eligibility-check

{
  "schemeId": "pm-kisan",
  "userProfile": { ... }
}
3. AI Integration (Amazon Bedrock)
Prompt Engineering:

Scheme Matching Prompt:
You are an expert on Indian government schemes. Based on the user profile:
- Age: {age}
- Income: {income}
- Occupation: {occupation}
- State: {state}
- Category: {category}

From the following schemes: {schemes_json}

Identify the top 3 most relevant schemes and explain why each matches in simple language.
Format your response as JSON with scheme details and eligibility reasoning.
Conversational Response Prompt:
You are a helpful assistant for Indian citizens seeking government schemes.
User question: {user_message}
User profile: {profile}
Available schemes: {relevant_schemes}

Respond in a friendly, helpful manner using simple language.
If the user asks about eligibility, explain clearly what documents they need.
Always end with asking if they need more information about any specific scheme.
4. Data Model
Scheme Data Structure:

{
  "id": "pm-kisan",
  "name": "PM-KISAN Samman Nidhi",
  "category": "agriculture",
  "type": "central",
  "description": "Financial assistance to small and marginal farmers",
  "benefits": {
    "amount": "₹6,000 per year",
    "installments": "3 installments of ₹2,000 each",
    "duration": "Ongoing"
  },
  "eligibility": {
    "occupation": ["farmer"],
    "landHolding": "up to 2 hectares",
    "income": ["below-poverty-line", "low-income"],
    "age": "18+",
    "excludedCategories": ["income-tax-payers"]
  },
  "documents": [
    "Aadhaar Card",
    "Bank Account Details",
    "Land Records (Khatauni/Khasra)"
  ],
  "applicationProcess": [
    "Visit PM-KISAN portal or nearest CSC",
    "Fill registration form with Aadhaar",
    "Upload land documents",
    "Verify bank account details",
    "Submit application and get acknowledgment"
  ],
  "officialWebsite": "https://pmkisan.gov.in",
  "states": ["all"],
  "tags": ["agriculture", "financial-assistance", "farmers"]
}
User Profile Model:

interface UserProfile {
  age: 'below-18' | '18-35' | '35-50' | '50-60' | 'above-60';
  income: 'below-poverty-line' | 'low-income' | 'middle-income' | 'high-income';
  occupation: 'farmer' | 'student' | 'senior-citizen' | 'woman' | 'entrepreneur' | 'unemployed' | 'other';
  state: string; // ISO state codes
  category: 'general' | 'sc' | 'st' | 'obc' | 'minority';
  hasDisability?: boolean;
  isRural?: boolean;
}
User Experience Design
Conversation Flow
Welcome & Onboarding

Bot: "Namaste! I'm BharatScheme AI. I help you find government schemes you're eligible for. 
     Let's start with a few simple questions about you."

User: [Clicks "Get Started" or types message]

Bot: "What's your age group?" 
     [Shows visual buttons: Below 18, 18-35, 35-50, 50-60, Above 60]
Profile Building

Bot: "What do you do for work?"
     [Visual cards with icons: Farmer, Student, Business Owner, etc.]

Bot: "Which state are you from?"
     [Dropdown with state names in local language]

Bot: "What's your family's income level?"
     [Simple categories with examples]
Scheme Discovery

User: "What schemes are available for me?"

Bot: "Based on your profile, I found 5 schemes perfect for you:
     
     🌾 PM-KISAN: ₹6,000/year for farmers
     📚 Scholarship Scheme: Education support
     🏠 Housing Scheme: Home loan assistance
     
     Which one would you like to know more about?"
Detailed Information

User: "Tell me about PM-KISAN"

Bot: "PM-KISAN gives ₹6,000 per year to small farmers like you.
     
     ✅ You're eligible because:
     - You're a farmer
     - Your land is under 2 hectares
     
     📋 You'll need:
     - Aadhaar Card
     - Bank details
     - Land papers
     
     Would you like step-by-step application help?"
Accessibility Features
Visual Design

High contrast colors (4.5:1 ratio minimum)
Large touch targets (44px minimum)
Clear typography (16px base font size)
Icon + text labels for all actions
Interaction Design

Keyboard navigation support
Screen reader compatibility
Voice input option
Simple language (Grade 8 reading level)
Progressive Enhancement

Works without JavaScript (basic form)
Offline capability for cached schemes
Slow network optimization
Technical Implementation
Frontend Technology Stack
React 18 with TypeScript
CSS Modules for styling
React Query for API state management
React Hook Form for form handling
Framer Motion for smooth animations
React Speech Kit for voice features
Backend Technology Stack
Node.js 18 with TypeScript
AWS Lambda for serverless compute
AWS API Gateway for REST API
Amazon Bedrock for AI capabilities
AWS S3 for data storage
AWS CloudWatch for monitoring
Deployment Architecture
# AWS CDK Stack
Resources:
  # Frontend
  WebsiteBucket:
    Type: AWS::S3::Bucket
    Properties:
      WebsiteConfiguration:
        IndexDocument: index.html
  
  CloudFrontDistribution:
    Type: AWS::CloudFront::Distribution
    Properties:
      DistributionConfig:
        Origins:
          - DomainName: !GetAtt WebsiteBucket.DomainName
        DefaultCacheBehavior:
          TargetOriginId: S3Origin
  
  # Backend
  SchemeMatcherFunction:
    Type: AWS::Lambda::Function
    Properties:
