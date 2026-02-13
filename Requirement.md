# Requirements Document
## Recommendation System for Cloud Storage Optimization

**Team:** Utilex  
**Team Leader:** Giriraj Bahati  
**Institution:** MIT School of Computing, Department of Computer Science & Engineering

---

## Problem Statement

Cloud storage systems rely on static, rule-based tiering policies that fail to distinguish between valuable authentic data and worthless AI-generated data with no business value in expensive storage tiers. Organizations waste significant costs by storing low-value synthetic content alongside critical business data. Current systems achieve only 30-50% cost savings, leaving 15-25% of potential savings untapped.

---

## Functional Requirements

### FR1: AI-Generated Data Detection
- **Description:** Detect AI-generated vs. authentic images using C2P-CLIP model
- **Acceptance Criteria:**
  - Detection accuracy ≥ 85%
  - Support for common image formats (JPEG, PNG, WebP)
  - Real-time detection capability (< 100ms per image)
  - Probability scoring (0.0-1.0 range)

### FR2: Access Pattern Analysis
- **Description:** Predict future data access using recency-based scoring
- **Acceptance Criteria:**
  - Calculate days since last access for each file
  - Convert to recency score (0-100 scale)
  - Formula: max(0, 100 - days × 2.0)
  - Update patterns in real-time

### FR3: Utility Score Calculation
- **Description:** Combine authenticity and recency metrics for tiering decisions
- **Acceptance Criteria:**
  - Weighted formula: (Recency_Score × 0.60) + (AI_Generated_Penalty × -0.40)
  - Generate scores for each data object
  - Support threshold customization

### FR4: Tier Recommendation Engine
- **Description:** Recommend optimal storage tier (Hot/Cold) based on utility scores
- **Acceptance Criteria:**
  - Hot Tier: Utility Score > 50 OR (Authentic data AND recent access)
  - Cold Tier: Utility Score ≤ 50 OR (AI-generated data)
  - Archive: AI-generated data with no recent access
  - Provide reasoning for recommendations

### FR5: Cloud Provider Integration
- **Description:** Seamless integration with major cloud storage providers
- **Acceptance Criteria:**
  - REST API for AWS S3, Azure Blob Storage, Google Cloud Storage
  - Metadata storage in MongoDB
  - API response time < 200ms
  - Support batch processing (1000+ files)

### FR6: Analytics & Reporting
- **Description:** Real-time visualization and reporting
- **Acceptance Criteria:**
  - Dashboard showing recommendations
  - Cost savings calculation and reporting
  - Historical trend analysis
  - Exportable reports (CSV, PDF)

---

## Non-Functional Requirements

### Performance
- API response time: < 200ms for single file recommendation
- Batch processing: < 5 seconds for 1000 files
- Accuracy: ≥ 85% AI detection
- Scalability: Support petabyte-scale data

### Reliability
- 99.5% uptime availability
- Graceful error handling
- Data backup and recovery mechanisms
- Monitoring and alerting

### Security
- API authentication (OAuth 2.0)
- Data encryption in transit (HTTPS)
- Secure credential management
- Access logging and auditing

### Usability
- Intuitive REST API documentation
- Clear error messages
- Simple configuration setup
- Minimal learning curve

---

## Technology Stack

### AI/ML
- Python 3.8+
- PyTorch (C2P-CLIP model)
- NumPy, Pandas

### Backend
- Flask (REST API)
- MongoDB (Metadata storage)
- Redis (Caching)

### Cloud
- AWS SDK (S3)
- Azure SDK (Blob Storage)
- Google Cloud SDK

### Development & Testing
- VS Code (IDE)
- Postman (API testing)
- Git (Version control)
- Docker (Containerization)

### Simulation & Analytics
- CloudSim (Algorithm simulation)
- PowerBI (Dashboard & reporting)

---

## Expected Outcomes

1. **AI Detection Accuracy:** ≥ 85% with C2P-CLIP model
2. **Cost Savings:** 45-60% (vs. current 30-50%)
3. **Performance:** Real-time recommendations (100ms)
4. **Scalability:** Support petabyte-scale data
5. **ROI:** Achievable within 6 months

---

## Success Metrics

| Metric | Target | Status |
|--------|--------|--------|
| AI Detection Accuracy | ≥ 85% | ✓ Achievable |
| Cost Savings | 45-60% | ✓ Achievable |
| API Response Time | < 200ms | ✓ Achievable |
| Uptime | 99.5% | ✓ Achievable |
| Multi-cloud Support | AWS, Azure, GCP | ✓ Achievable |

---

## Team Responsibilities

- **Giriraj Bahati (Lead):** Project management, integration, and overall coordination
- **Team Members:** Research, implementation, testing, and deployment

---

## Timeline

- **Week 1-2:** Dataset collection & C2P-CLIP setup
- **Week 2-3:** Model integration & testing
- **Week 3-4:** Recency scoring & utility formula
- **Week 4-5:** Tier recommendation engine
- **Week 5-6:** Testing & optimization
- **Week 6-7:** Cloud integration & deployment
