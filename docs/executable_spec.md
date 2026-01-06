# Executable Product Specification: Wealth Companion

**Version**: 1.0 (v1 Decisions Finalized)
**Status**: Ready for Engineering Estimation
**Last Updated**: 2026-01-06

---

## v1 Product Decisions Summary

The following critical decisions have been made to unblock engineering:

| Decision Area | v1 Choice | Rationale |
|--------------|-----------|-----------|
| **Account Aggregation** | Plaid | Fastest integration, good coverage, strong developer experience |
| **Regulatory Posture** | Non-RIA, SEC/FINRA awareness | Read-only analytics product, no advice/recommendations |
| **Liquidity Buckets** | 3 buckets: < 30d, 30-90d, > 90d | Good enough for v1 questions, avoids over-modeling |
| **Scenarios** | Market downturn only (equity decline %) | Single scenario for v1, add others based on usage |
| **Illiquid Assets** | Out (manual placeholder only) | Excluded from calculations and scenarios |
| **Platform** | Web only (desktop-first) | Faster build, better for complex financial views |
| **UX Constraints** | No gamification, calm design, neutral language | Information-dense, professional, low visual noise |

**All critical blockers resolved. Engineering can begin estimation and implementation.**

---

## 1. Problem Statement

High net worth individuals (HNW) maintain assets across multiple financial institutions with no unified view to answer basic questions about their financial position. Current solutions require manual spreadsheets, phone calls, and effort to answer questions like:
- Where am I exposed (US vs global)?
- How liquid am I if I need cash soon?
- What happens in a market downturn?

Existing tools either feel retail-oriented or bank-controlled. Users need a trusted financial co-pilot that provides clarity without replacing their advisors.

## 2. Scope

### In Scope
- Unified net worth dashboard aggregating data from US brokerage and bank accounts
- Asset mix visualization
- Simple scenario modeling capabilities
- AI-powered explanations in plain language (NOT financial advice)
- Support for US high net worth individuals
- Account aggregation from US financial institutions

### Explicitly Out of Scope
- Financial advice or recommendations
- Mass market / retail users
- Trading functionality
- Advisor replacement features
- Offshore assets (deferred to future phase)
- **Illiquid assets in v1**: Manual placeholder only - users can note existence but excluded from calculations and scenarios
- Mobile applications (v1 is web-only, desktop-first)

### Deferred Decisions (Non-Blocking)
- **Business model**: Subscription vs advisor-mediated vs white-label (does not affect v1 build)

## 3. Key Assumptions

### Product Assumptions
- **ASSUMPTION**: MVP focuses on read-only aggregation and visualization; no transaction capabilities
- **ASSUMPTION**: Users will self-connect their accounts (vs advisor-mediated setup)
- **ASSUMPTION**: "Net worth view" means total assets minus liabilities with drill-down by institution/account
- **ASSUMPTION**: "Asset mix view" means breakdown by standard asset classes (equities, fixed income, cash, alternatives)
- **ASSUMPTION**: "Simple scenarios" means what-if analysis with 1-3 variables (e.g., market drop %, time horizon)

### User Assumptions
- **ASSUMPTION**: Target users have 5-20 financial accounts across 3-10 institutions
- **ASSUMPTION**: Users are comfortable granting read-only API access to their financial accounts
- **ASSUMPTION**: Users expect daily data refresh (not real-time)

### Data Assumptions
- **DECISION**: Account aggregation via Plaid (rationale: fastest integration, good coverage, strong developer experience)
- **ASSUMPTION**: Plaid APIs support major US brokerages and banks needed for target users
- **ASSUMPTION**: Asset valuations are sourced from Plaid; no independent valuation
- **DECISION**: Illiquid assets excluded from v1 calculations; manual placeholder field only

### Technical Assumptions
- **ASSUMPTION**: Cloud-based deployment (AWS/GCP/Azure)
- **ASSUMPTION**: AI explanations powered by LLM with guardrails against advice
- **ASSUMPTION**: Data encrypted at rest and in transit
- **DECISION**: Web application only (desktop-first), modern browsers (Chrome, Firefox, Safari, Edge)

## 4. Functional Requirements

### FR-1: Account Aggregation
1.1. User can securely connect US brokerage and bank accounts via OAuth or credentials
1.2. System must support reconnection flow when authentication expires
1.3. System must display connection status (active, disconnected, error) per account
1.4. System must refresh account data at least daily

### FR-2: Net Worth Dashboard
2.1. Display total net worth (assets - liabilities) with as-of timestamp
2.2. Display net worth trend over time (minimum: 1 month, 6 months, 1 year, all time)
2.3. Break down assets by institution with account-level detail
2.4. Break down liabilities by institution with account-level detail
2.5. Display currency in USD only (v1)

### FR-3: Asset Mix Visualization
3.1. Display asset allocation by asset class (equities, fixed income, cash, alternatives, other)
3.2. Display geographic exposure (US vs international) where data available
3.3. Display liquidity breakdown using 3 buckets:
     - **< 30 days**: Cash, money market, checking/savings accounts
     - **30-90 days**: Publicly traded securities (stocks, bonds, ETFs)
     - **> 90 days**: Alternatives, real estate, other holdings
     (Note: Illiquid assets placeholder-only in v1, not included in calculations)

### FR-4: Scenario Modeling (v1: Single Scenario Only)
4.1. User can model market downturn scenario (specify % decline in equities, e.g., -20%)
4.2. System calculates projected impact on total net worth (apply % decline to equity holdings only)
4.3. System displays results clearly with caveats about model limitations
4.4. Additional scenarios (if any) deferred to post-v1

### FR-5: AI Explanations
5.1. User can request plain-language explanation of their net worth composition
5.2. User can request plain-language explanation of their asset mix
5.3. System must NOT provide financial advice, recommendations, or predictions
5.4. All AI responses must include disclaimer about not constituting advice
5.5. AI explanations must be auditable/logged for compliance review

## 5. Non-Functional Constraints

### Security & Privacy
- Must encrypt all data at rest and in transit (minimum TLS 1.2)
- Must not store raw financial credentials
- Must support account deletion with full data purge
- Must pass SOC 2 Type II audit **[ASSUMPTION - not stated but implied by "security critical"]**

### Regulatory Compliance
- **Posture**: Non-RIA (Registered Investment Advisor) information and analytics product
- **Awareness**: Design with SEC and FINRA regulations in mind, but product is read-only with no advice or recommendations
- Must include prominent disclaimers that tool does not provide financial advice
- AI explanations must be explainable (no pure black-box outputs)
- Legal review required before launch to confirm non-advisory classification

### UX Constraints (v1 "Not Retail App" Definition)
- **No gamification**: No badges, streaks, congratulatory messages, or engagement hooks
- **No action encouragement**: No charts/messaging designed to encourage frequent trading or changes
- **Neutral language**: Professional tone, low visual noise, information-dense layouts
- **Calm design**: Subdued color palette, minimal animations, focus on clarity over excitement
- Accuracy prioritized over speed (acceptable to show stale data with timestamp vs real-time errors)

### Performance
- Dashboard must load within 3 seconds **[ASSUMPTION - no target specified]**
- Data refresh latency acceptable up to 24 hours

## 6. System Notes

### Data Sources
- **Account aggregation**: Plaid (Link SDK for OAuth, API for data retrieval)
- **Asset pricing**: From Plaid; no independent pricing for v1
- **Institution coverage**: Verify Plaid supports target institutions (Fidelity, Schwab, Vanguard, major banks)

### AI Implementation
- LLM-based (model TBD: GPT-4, Claude, or other)
- Requires prompt engineering to prevent advice generation
- Requires input validation to reject advice-seeking queries
- All AI interactions logged for compliance review

### Platform & Browser Support
- **Platform**: Web application (desktop-first, responsive down to tablet)
- **Browser support**: Modern evergreen browsers (Chrome, Firefox, Safari, Edge - latest 2 versions)
- **Mobile**: Out of scope for v1

## 7. Edge Cases & Risks

### Edge Cases
1. **Account disconnection**: What happens when Plaid link breaks? → Show last known data with warning banner and reconnect CTA
2. **Multi-currency accounts**: User has foreign currency holdings → Convert to USD using current exchange rate, display converted amount
3. **Duplicate accounts**: Same account appears via multiple Plaid connections → Dedupe by account number + institution
4. **Advice boundary**: User asks AI "Should I sell my stocks?" → Gracefully decline with message: "I can explain your holdings, but I can't provide investment advice. Consider consulting your financial advisor."
5. **Negative net worth**: User has more liabilities than assets → Display negative value clearly without judgment
6. **Partial data availability**: Some accounts missing geographic or asset class data → Show "Unknown" category, calculate percentages from known data only

### Major Risks
- **Aggregation reliability**: If Plaid connections frequently break, product becomes unusable → Mitigation: Monitor connection health, improve reconnection UX
- **Regulatory classification**: AI explanations could trigger RIA registration requirements → Mitigation: Legal review before launch, strict no-advice guardrails
- **Data accuracy**: Errors in net worth calculations could break trust permanently → Mitigation: Reconciliation checks, prominent data source attribution, user-reported error feedback
- **Plaid coverage gaps**: Target institutions not fully supported → Mitigation: Verify coverage during provider integration, plan manual entry fallback
- **Compliance drift**: Features added without regulatory review could create liability → Mitigation: Legal checkpoint for all feature additions

## 8. Acceptance Criteria

### Minimum Viable Product (MVP)
User can:
1. Connect at least 3 major US financial institutions via Plaid (e.g., Fidelity, Schwab, Chase)
2. View total net worth updated within 24 hours of account connection
3. See net worth trend over past 6 months
4. View asset mix by asset class (equities, fixed income, cash, alternatives, other)
5. View liquidity breakdown (< 30 days, 30-90 days, > 90 days)
6. Run a basic market downturn scenario (equities down X%, e.g., -20%)
7. Request AI explanation of their portfolio composition
8. See clear disclaimers that tool provides no advice
9. Access via modern web browser (desktop-first experience)

Success Metrics:
- User checks app at minimum weekly (target frequency TBD)
- User perceives app as "reference point" for financial position **[Requires measurement approach]**

## 9. Open Questions (Non-Blocking)

### Important for Planning (Not Blockers)
1. **Data retention policy**: How long to store historical account data? (Affects storage costs, compliance)
2. **Measurement approach**: How to measure "user perceives app as reference point"? (Affects success metrics)
3. **Customer support level**: Live chat? Email only? Self-service? (Affects error handling UX and costs)
4. **Target launch date**: Is there a deadline driving scope? (Affects prioritization and staffing)
5. **LLM provider choice**: GPT-4, Claude, or other? (Affects cost, latency, compliance review needs)

### Deferred to Implementation
6. What happens if Plaid changes asset classifications vs user expectations? (Handle during implementation, may need user override feature)
7. Should users be able to manually adjust asset classifications? (Nice-to-have, not required for v1)
8. Reconnection notification strategy: Email, in-app, or both? (UX decision during implementation)

---

## Notes for Engineering Estimation

### ✅ Ready to Estimate (All Blockers Resolved)

All v1 functional requirements are now estimable:

1. **Plaid integration** - Account linking, OAuth flow, data refresh
2. **Net worth dashboard** - Aggregation, trending (6mo), drill-down by institution/account
3. **Asset mix visualization** - Asset class breakdown, geographic exposure, liquidity buckets (< 30d, 30-90d, > 90d)
4. **Scenario modeling** - Single scenario (equity downturn %), impact calculation
5. **AI explanations** - LLM integration, no-advice guardrails, logging for compliance
6. **Web application** - Desktop-first responsive design, modern browser support
7. **UX constraints** - "Not retail" design system (calm, professional, no gamification)
8. **Compliance features** - Disclaimers, non-advisory posture, explainable AI

### Recommended Sprint Planning Approach

**Phase 1: Foundation (Estimate First)**
- Plaid integration and account connection flow
- Data model (accounts, holdings, valuations, historical snapshots)
- Basic net worth calculation and display
- Authentication and user management

**Phase 2: Core Views (Estimate After Phase 1 Complete)**
- Net worth trending over time
- Asset mix visualizations (asset class, geography, liquidity)
- Account drill-down and detail views

**Phase 3: Advanced Features (Can Estimate in Parallel)**
- Scenario modeling (market downturn)
- AI explanation integration
- Compliance logging and disclaimers

**Phase 4: Polish (Estimate Last)**
- "Not retail" design system implementation
- Error handling and edge cases
- Reconnection flows
- Performance optimization

### External Dependencies (Not Engineering Blockers)
- Plaid account setup and API keys (procurement/legal)
- LLM provider selection and API access
- Legal review for non-RIA classification (before launch)
- Design system for "calm, professional" aesthetic (can proceed with wireframes)

### Engineering Can Start Immediately On:
1. Plaid SDK evaluation and proof-of-concept
2. Data model design
3. Architecture planning (cloud provider, framework, database)
4. Security requirements (encryption, credential storage, data purge)
