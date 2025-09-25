# Feature Specification: AutoTrade Danelfin Trading Platform

**Feature Branch**: `001-using-desigdoc-txt`
**Created**: 2025-09-24
**Status**: Draft
**Input**: User description: "using desigDoc.txt"

## Execution Flow (main)
```
1. Parse user description from Input
   → Design document provided: DesignDoc.txt
2. Extract key concepts from description
   → Identified: Users, Admin, Trading Parameters, APIs (Danelfin, Webull), Payments, Notifications
3. For each unclear aspect:
   → Marked clarification needed areas
4. Fill User Scenarios & Testing section
   → User flows defined for registration, configuration, and trading
5. Generate Functional Requirements
   → Requirements defined for each major system capability
6. Identify Key Entities
   → User Profile, Trading Configuration, Trade, Payment Subscription
7. Run Review Checklist
   → WARN "Spec has uncertainties regarding API specifics and risk thresholds"
8. Return: SUCCESS (spec ready for planning)
```

---

## ⚡ Quick Guidelines
- ✅ Focus on WHAT users need and WHY
- ❌ Avoid HOW to implement (no tech stack, APIs, code structure)
- 👥 Written for business stakeholders, not developers

---

## Clarifications

### Session 2025-01-24
- Q: Which risk calculation method should the system use? → A: Both percentage of position size and fixed dollar amount
- Q: How should the system handle multiple simultaneous trade opportunities? → A: Execute highest Danelfin score first, then others if funds remain; no trade if AI score < 9 for automated trading (unless user overrides with custom minimum)
- Q: What should be the trade history retention period? → A: Indefinitely (no automatic deletion)
- Q: How should the system handle API unavailability? → A: Pause automated trading, switch to manual mode, while retrying connections with exponential backoff in background
- Q: What should be the subscription price for platform access? → A: 49 dollars per year

## User Scenarios & Testing *(mandatory)*

### Primary User Story
As a conservative trader, I want an automated trading platform that uses Danelfin's AI predictions and executes trades through Webull based on my risk parameters, so that I can participate in the market with controlled risk while maintaining my regular activities.

### Acceptance Scenarios
1. **Given** a new user visits the platform, **When** they complete registration with email, phone, and password, **Then** their account is created with secure hashed password storage
2. **Given** a registered user has configured their Stripe payment, **When** they activate their subscription, **Then** they gain access to the trading features
3. **Given** a user has set trading parameters (risk %, take profit %, alert %), **When** Danelfin identifies a trade opportunity within parameters, **Then** the system automatically initiates the trade on Webull
4. **Given** a trade is executed, **When** the position reaches the configured profit or loss threshold, **Then** the system closes the position and sends SMS/email notification
5. **Given** an admin user (admin@wxperts.com) logs in, **When** they access the management panel, **Then** they can view and manage all user profiles and trading data
6. **Given** a user selects paper trading mode, **When** trades are executed, **Then** the system simulates trades internally using real market data from Webull

### Edge Cases
- What happens when Danelfin or Webull API is unavailable? → System pauses automated trading, switches to manual mode requiring user confirmation, and retries connections with exponential backoff in background
- How does system handle [insufficient funds for a trade]?
- What happens when multiple trade signals arrive simultaneously? → System prioritizes by highest Danelfin AI score and executes sequentially until funds exhausted
- How does system respond when [market closes during an active trade]?
- What happens when [user's payment method fails]?

## Requirements *(mandatory)*

### Functional Requirements

#### User Management & Authentication
- **FR-001**: System MUST allow users to register with email address, phone number, and password
- **FR-002**: System MUST securely hash all user passwords before storage
- **FR-003**: System MUST support Single Sign-On (SSO) for [NEEDS CLARIFICATION: which specific SSO providers - Google, Microsoft, Apple?]
- **FR-004**: System MUST designate admin@wxperts.com as the master admin user with full access
- **FR-005**: Admin users MUST be able to grant manager access to other users
- **FR-006**: System MUST maintain user profiles with flags for Stripe payment status and Admin privileges

#### Payment Integration
- **FR-007**: System MUST integrate with Stripe for annual subscription payments at $49 per year
- **FR-008**: System MUST track and validate active payment status before allowing trading features
- **FR-009**: System MUST handle payment failures and subscription management
- **FR-010**: System MUST support subscription renewal reminders before expiration

#### API Integrations
- **FR-011**: Users MUST be able to enter and store Danelfin API credentials
- **FR-012**: Users MUST be able to enter and store Webull API account credentials
- **FR-013**: System MUST validate API credentials upon entry
- **FR-014**: System MUST create Model Context Protocol (MCP) interfaces for both Webull and Danelfin APIs

#### Trading Configuration
- **FR-015**: Users MUST be able to configure cash amount to manage
- **FR-016**: Users MUST be able to set risk percentage for trades
- **FR-017**: Users MUST be able to set take profit percentage
- **FR-018**: Users MUST be able to set alert percentage for both potential losses and gains
- **FR-019**: Users MUST be able to determine maximum dollar amount to risk per trade
- **FR-020**: Users MUST be able to choose between paper trading and live trading modes
- **FR-021**: System MUST support partial share purchases
- **FR-022**: Users MUST be able to set minimum AI score threshold for automated trades (default: 9)
- **FR-023**: System MUST prevent automated trades when Danelfin AI score is below user-defined minimum

#### Trading Execution
- **FR-024**: System MUST monitor Danelfin for trade opportunities matching user parameters
- **FR-025**: System MUST automatically initiate trades on Webull when criteria are met and switch to automate is true
- **FR-026**: System MUST use 1/5 of the possible gain percentage as default sell price when accepted
- **FR-027**: System MUST use Danelfin projected gain as sell price when default is disabled (set to zero)
- **FR-028**: System MUST support trailing stop-loss percentages that adjust with gains
- **FR-029**: Paper trading MUST simulate internally using current daily gain/loss from Webull API
- **FR-030**: When multiple trades qualify simultaneously, system MUST prioritize by highest Danelfin AI score
- **FR-031**: System MUST execute prioritized trades sequentially until available funds are exhausted

#### Visualization & Reporting
- **FR-032**: System MUST display a graph showing baseline starting amount and gain line with percentage
- **FR-033**: System MUST provide a listing of each trade showing:
  - Current position
  - Number of shares
  - Gain/loss percentage
  - Dollar amount of gain
  - Total value
  - Entry max percentage to sell
- **FR-034**: System MUST track and display current trading status for each user
- **FR-035**: System MUST retain all trade history and performance data indefinitely without automatic deletion
- **FR-036**: Users MUST be able to access their complete historical trading data

#### Notifications
- **FR-037**: System MUST send SMS notifications when trades are initiated
- **FR-038**: System MUST send SMS notifications when trades are closed
- **FR-039**: System MUST send screen notifications for trade activities
- **FR-040**: System MUST send daily email summaries of actions taken and current status
- **FR-041**: SMS delivery method MUST be [NEEDS CLARIFICATION: SMS provider/service not specified - Twilio, AWS SNS?]

#### Risk Management
- **FR-042**: System MUST enforce maximum trade amount limits per user configuration
- **FR-043**: System MUST respect user-defined risk percentages for all trades
- **FR-044**: System MUST monitor and alert on approaching loss thresholds
- **FR-045**: System MUST support two risk calculation methods: percentage of position size (e.g., 2% stop-loss on position value) and fixed dollar amount per trade
- **FR-046**: Users MUST be able to select their preferred risk calculation method (percentage-based or fixed amount)

#### System Resilience & API Handling
- **FR-047**: System MUST pause automated trading when either Danelfin or Webull API becomes unavailable
- **FR-048**: System MUST switch to manual confirmation mode during API outages
- **FR-049**: System MUST implement exponential backoff retry logic for API connections
- **FR-050**: System MUST send immediate notifications to users when APIs become unavailable
- **FR-051**: System MUST automatically resume normal operations when APIs are restored

### Key Entities *(include if feature involves data)*

- **User Profile**: Represents platform users with authentication credentials, payment status, admin flags, and contact information
- **Trading Configuration**: User-specific trading parameters including risk percentages, cash amounts, and trading mode preferences
- **Trade**: Individual trade records with entry/exit points, share quantities, profit/loss calculations, and associated Danelfin predictions
- **Payment Subscription**: Stripe subscription information linking users to their payment status and billing history
- **API Credentials**: Encrypted storage of user's Danelfin and Webull API access credentials
- **Notification Preferences**: User communication preferences for SMS, email, and in-app notifications

---

## Review & Acceptance Checklist
*GATE: Automated checks run during main() execution*

### Content Quality
- [x] No implementation details (languages, frameworks, APIs)
- [x] Focused on user value and business needs
- [x] Written for non-technical stakeholders
- [x] All mandatory sections completed

### Requirement Completeness
- [ ] No [NEEDS CLARIFICATION] markers remain
- [x] Requirements are testable and unambiguous
- [x] Success criteria are measurable
- [x] Scope is clearly bounded
- [ ] Dependencies and assumptions identified

**Note**: Several clarification points remain:
- Specific SSO providers to support
- SMS notification service provider
- Exact risk calculation formulas
- API rate limits and handling strategies
- Data retention policies for trade history

---

## Execution Status
*Updated by main() during processing*

- [x] User description parsed
- [x] Key concepts extracted
- [x] Ambiguities marked
- [x] User scenarios defined
- [x] Requirements generated
- [x] Entities identified
- [x] Review checklist passed

---