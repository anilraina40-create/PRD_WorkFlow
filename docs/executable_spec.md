# Executable Product Specification: Wealth Companion

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

### Unclear / Requires Decision
- **Illiquid assets**: Described as "messy" but not explicitly in or out of scope
- **Platform**: Web vs mobile vs both (not specified)
- **Business model**: Subscription vs advisor-mediated vs white-label (undecided, may affect UX/scope)

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
- **ASSUMPTION**: Account aggregation via third-party service (e.g., Plaid, Yodlee, or similar)
- **ASSUMPTION**: Aggregation APIs support major US brokerages and banks
- **ASSUMPTION**: Asset valuations are sourced from aggregation provider; no independent valuation
- **ASSUMPTION**: Illiquid assets (if supported) require manual entry

### Technical Assumptions
- **ASSUMPTION**: Cloud-based deployment (AWS/GCP/Azure)
- **ASSUMPTION**: AI explanations powered by LLM with guardrails against advice
- **ASSUMPTION**: Data encrypted at rest and in transit

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
3.3. Display liquidity breakdown (highly liquid, moderately liquid, illiquid) **[Requires definition of liquidity buckets]**

### FR-4: Scenario Modeling
4.1. User can model market downturn scenario (specify % decline in equities)
4.2. System calculates projected impact on total net worth
4.3. System displays results clearly with caveats about model limitations
**[Requires specification: What other scenarios? What variables? What calculations?]**

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
- Must comply with applicable financial regulations **[Requires specification: which regulations? SEC, FINRA, state-level?]**
- Must include disclaimers that tool does not provide financial advice
- AI explanations must be explainable (no pure black-box outputs)

### UX Constraints
- Must not "feel like a retail app" **[Requires concrete definition: professional design system, no gamification, specific tone?]**
- Accuracy prioritized over speed (acceptable to show stale data with timestamp vs real-time errors)

### Performance
- Dashboard must load within 3 seconds **[ASSUMPTION - no target specified]**
- Data refresh latency acceptable up to 24 hours

## 6. System Notes

### Data Sources
- Account aggregation via **[TBD: Plaid, Yodlee, Finicity, or other?]**
- Asset pricing from aggregation provider (no independent pricing for v1)

### AI Implementation
- LLM-based (model TBD: GPT-4, Claude, or other)
- Requires prompt engineering to prevent advice generation
- Requires input validation to reject advice-seeking queries

### Platform
- **[TBD: Web app, mobile app, or both?]**
- **[TBD: Browser requirements if web? iOS/Android versions if mobile?]**

## 7. Edge Cases & Risks

### Edge Cases
1. **Account disconnection**: What happens when account link breaks? (Show last known data with warning? Hide account?)
2. **Multi-currency accounts**: User has foreign currency holdings - how to display? (Convert to USD? Show separately?)
3. **Stale valuations**: Illiquid asset hasn't been valued in 6+ months - how to handle?
4. **Duplicate accounts**: Same account appears via multiple aggregation paths - how to dedupe?
5. **Advice boundary**: User asks AI "Should I sell my stocks?" - how to gracefully decline while remaining helpful?

### Major Risks
- **Aggregation reliability**: If account connections frequently break, product becomes unusable
- **Regulatory classification**: Unclear if AI explanations trigger advisory registration requirements
- **Data accuracy**: Errors in net worth calculations could break trust permanently
- **Illiquid asset valuation**: No clear path to accurate pricing for alternatives/private holdings
- **Compliance gaps**: "Regulation exists but isn't fully thought through" - risk of building non-compliant features

## 8. Acceptance Criteria

### Minimum Viable Product (MVP)
User can:
1. Connect at least 3 major US financial institutions (e.g., Fidelity, Schwab, Chase)
2. View total net worth updated within 24 hours of account connection
3. See net worth trend over past 6 months
4. View asset mix by asset class
5. Run a basic market downturn scenario (equities down X%)
6. Request AI explanation of their portfolio composition
7. See clear disclaimers that tool provides no advice

Success Metrics:
- User checks app at minimum weekly (target frequency TBD)
- User perceives app as "reference point" for financial position **[Requires measurement approach]**

## 9. Open Questions (Blockers)

### Critical Path Questions
1. **Which account aggregation provider?** (Affects cost, coverage, reliability, timeline)
2. **What specific regulations apply?** (SEC Reg BI? State RIA rules? Determines compliance scope)
3. **How are liquidity buckets defined?** (Required for FR-3.3)
4. **What is the complete list of scenarios for v1?** (Currently only market downturn specified)
5. **Are illiquid assets in or out for v1?** If in, manual entry only or partial automation?
6. **Web, mobile, or both?** (Determines tech stack and resourcing)
7. **What constitutes "not feeling like a retail app"?** (Need design system constraints, tone guide, feature constraints)

### Important but Non-Blocking
8. What is the data retention policy?
9. What happens if aggregation provider changes asset classifications vs user expectations?
10. How should the system handle accounts with negative values (margin, loans)?
11. What level of customer support is planned? (Affects error handling UX)
12. Is there a target launch date? (Affects scope prioritization)

---

## Notes for Engineering Estimation

**Ready to estimate:**
- Account aggregation integration (pending provider selection)
- Basic net worth dashboard and trending
- Asset class visualization
- Single scenario modeling (market downturn)

**Needs refinement before estimation:**
- AI explanation feature (scope of queries, guardrail implementation, logging requirements)
- Liquidity analysis (pending bucket definitions)
- Additional scenarios beyond market downturn
- Compliance implementation (pending regulatory guidance)
- Illiquid asset handling (pending scope decision)

**Recommend starting with:**
1. Provider evaluation and selection (aggregation service)
2. Regulatory/compliance consultation
3. UX definition workshop for "not retail" constraint
4. Data model design for accounts, assets, scenarios
