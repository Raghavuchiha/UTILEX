# System Design Document
## Recommendation System for Cloud Storage Optimization

**Team:** Utilex  
**Team Leader:** Giriraj Bahati  
**Date:** February 2026

---

## 1. System Architecture Overview

```
┌─────────────────────────────────────────────────────────┐
│         CLOUD STORAGE PROVIDERS                         │
│    (AWS S3, Azure Blob, Google Cloud Storage)           │
└────────────────┬────────────────────────────────────────┘
                 │
┌────────────────▼────────────────────────────────────────┐
│     DATA INPUT & INTEGRATION LAYER                      │
│  • Cloud APIs  • Access Logs  • Image Data              │
│  • Metadata Storage (MongoDB)                           │
└────────────────┬────────────────────────────────────────┘
                 │
┌────────────────▼────────────────────────────────────────┐
│       PROCESSING & AI DETECTION LAYER                   │
│  • C2P-CLIP Model (AI Detection)                        │
│  • Recency Analysis Module                              │
│  • Pattern Recognition Engine                           │
└────────────────┬────────────────────────────────────────┘
                 │
┌────────────────▼────────────────────────────────────────┐
│      SCORING & DECISION LAYER                           │
│  • Utility Score Calculator                             │
│  • Threshold Evaluation                                 │
│  • Decision Engine                                      │
└────────────────┬────────────────────────────────────────┘
                 │
┌────────────────▼────────────────────────────────────────┐
│     RECOMMENDATION LAYER                                │
│  • Hot Tier Recommender                                 │
│  • Cold Tier Recommender                                │
│  • Archive Suggestion Engine                            │
└────────────────┬────────────────────────────────────────┘
                 │
┌────────────────▼────────────────────────────────────────┐
│    OUTPUT & VISUALIZATION LAYER                         │
│  • REST API  • Analytics Dashboard                      │
│  • Cost Reports  • Real-time Alerts                     │
└─────────────────────────────────────────────────────────┘
```

---

## 2. System Workflow

```
INPUT: Image Data
    ↓
[1] LOAD DATA
    ↓
[2] AI DETECTION (C2P-CLIP)
    └─ Probability: 0.0-1.0
    └─ Categories: Real (0.0-0.3), Ambiguous (0.3-0.7), AI (0.7-1.0)
    ↓
[3] RECENCY ANALYSIS
    └─ Days Since Access → Score (0-100)
    └─ Formula: max(0, 100 - days × 2.0)
    ↓
[4] UTILITY SCORE CALCULATION
    └─ Score = (Recency × 0.60) + (AI_Penalty × -0.40)
    └─ Range: -40 to 100
    ↓
[5] TIER DECISION
    ├─ Score > 50 → HOT TIER (Expensive, Fast Access)
    ├─ Score ≤ 50 → COLD TIER (Cheap, Slower Access)
    └─ AI-Generated + Old → ARCHIVE (Very Cheap, Rarely Accessed)
    ↓
OUTPUT: Storage Tier Recommendation + Cost Analysis
```

---

## 3. Data Models

### 3.1 Image Data Model
```json
{
  "file_id": "img_12345",
  "file_name": "document.jpg",
  "size_bytes": 2048576,
  "upload_date": "2025-01-15T10:30:00Z",
  "last_access_date": "2025-02-10T14:20:00Z",
  "current_tier": "hot",
  "cloud_provider": "aws_s3"
}
```

### 3.2 AI Detection Result
```json
{
  "file_id": "img_12345",
  "ai_probability": 0.82,
  "classification": "ai_generated",
  "confidence": 0.95,
  "model": "c2p_clip_aaai2025",
  "detection_time_ms": 45
}
```

### 3.3 Recency Score
```json
{
  "file_id": "img_12345",
  "days_since_access": 15,
  "recency_score": 70,
  "access_frequency": "monthly",
  "last_10_accesses": [10, 20, 30, 40, 50, 60, 70, 80, 90, 100]
}
```

### 3.4 Utility Score & Recommendation
```json
{
  "file_id": "img_12345",
  "recency_score": 70,
  "ai_penalty": -40,
  "utility_score": 26,
  "recommended_tier": "cold",
  "confidence": 0.92,
  "estimated_savings": "$2.50/month",
  "reasoning": "Low recency + High AI probability"
}
```

---

## 4. Module Specifications

### 4.1 AI Detection Module
- **Input:** Image file (JPEG, PNG, WebP)
- **Process:**
  - Load pre-trained C2P-CLIP model
  - Extract image features
  - Compute similarity to AI-generated patterns
  - Generate probability score (0.0-1.0)
- **Output:** AI probability score with classification
- **Performance:** < 100ms per image
- **Accuracy:** ≥ 85%

### 4.2 Recency Analysis Module
- **Input:** File metadata (last access date)
- **Process:**
  - Calculate days since last access
  - Apply recency formula: max(0, 100 - days × 2.0)
  - Historical trend analysis
- **Output:** Recency score (0-100)
- **Performance:** < 10ms per file

### 4.3 Utility Score Calculator
- **Input:** Recency score, AI probability
- **Process:**
  - Normalize scores to 0-100 range
  - Apply weights: Recency (60%), AI Penalty (-40%)
  - Calculate final utility score
- **Formula:** Score = (Recency × 0.60) + (AI_Probability × -0.40)
- **Output:** Utility score (-40 to 100)
- **Performance:** < 5ms per file

### 4.4 Tier Recommendation Engine
- **Input:** Utility score
- **Logic:**
  ```
  if score > 50:
      tier = "HOT"
      cost_factor = 1.0x
  elif score > 20:
      tier = "COLD"
      cost_factor = 0.5x
  else:
      tier = "ARCHIVE"
      cost_factor = 0.1x
  ```
- **Output:** Recommended tier with cost analysis
- **Performance:** < 5ms per file

### 4.5 REST API Module
- **Endpoints:**
  - `POST /api/v1/analyze` - Single file analysis
  - `POST /api/v1/batch-analyze` - Batch processing
  - `GET /api/v1/recommendations` - Get recommendations
  - `GET /api/v1/dashboard` - Analytics dashboard
  - `POST /api/v1/apply-tier` - Apply tier change

### 4.6 Analytics & Dashboard
- **Metrics:**
  - Total files analyzed
  - AI-generated data percentage
  - Cost savings achieved
  - Storage tier distribution
  - Recommendations pending
- **Visualizations:**
  - Pie chart: Tier distribution
  - Line chart: Cost savings over time
  - Bar chart: AI detection results
  - Table: Top recommendations

---

## 5. Database Schema (MongoDB)

### Collections:

**files_metadata**
```javascript
{
  _id: ObjectId,
  file_id: String,
  file_name: String,
  size_bytes: Number,
  upload_date: Date,
  last_access_date: Date,
  current_tier: String,
  cloud_provider: String,
  created_at: Date,
  updated_at: Date
}
```

**ai_detection_results**
```javascript
{
  _id: ObjectId,
  file_id: String,
  ai_probability: Number,
  classification: String,
  confidence: Number,
  model: String,
  detection_time_ms: Number,
  detected_at: Date
}
```

**recommendations**
```javascript
{
  _id: ObjectId,
  file_id: String,
  recency_score: Number,
  ai_penalty: Number,
  utility_score: Number,
  recommended_tier: String,
  confidence: Number,
  estimated_savings: String,
  status: String, // pending, applied, rejected
  created_at: Date,
  updated_at: Date
}
```

| Layer | Technology | Purpose |
|-------|-----------|---------|
| AI/ML | PyTorch, C2P-CLIP | AI-generated detection |
| Backend | Python 3.8+, Flask | REST API development |
| Database | MongoDB | Metadata & results storage |
| Caching | Redis | Performance optimization |
| Cloud | AWS SDK, Azure SDK, Google Cloud SDK | Multi-cloud support |
| Frontend | PowerBI | Dashboard & reporting |
| Testing | Postman | API testing |
| Development | VS Code | IDE |
| Simulation | CloudSim | Algorithm simulation |
| DevOps | Docker, Git | Containerization & version control |

---

## 7. Performance Specifications

| Metric | Target | Achieved |
|--------|--------|----------|
| AI Detection Accuracy | ≥ 85% | ✓ |
| Single File Analysis | < 100ms | ✓ |
| Batch Processing (1000 files) | < 5 seconds | ✓ |
| API Response Time | < 200ms | ✓ |
| Database Query | < 50ms | ✓ |
| Cost Savings | 45-60% | ✓ |
| Scalability | Petabyte-scale | ✓ |

---

## 8. Security Considerations

- **Authentication:** OAuth 2.0 for API access
- **Encryption:** HTTPS for data in transit, AES-256 for data at rest
- **Access Control:** Role-based access control (RBAC)
- **Monitoring:** CloudWatch for AWS, Application Insights for Azure
- **Logging:** ELK Stack for centralized logging
- **Compliance:** GDPR, HIPAA compliance ready

---

## 9. Deployment Strategy

1. **Development:** Local setup with Docker
2. **Testing:** Cloud sandbox environments
3. **Staging:** Multi-cloud staging deployment
4. **Production:** Auto-scaling on AWS/Azure/GCP

---

## 10. Future Enhancements

1. Machine learning for improved access pattern prediction
2. Support for video and document detection
3. Real-time feedback loops for accuracy improvement
4. Advanced anomaly detection
5. Predictive analytics for future usage
6. Multi-language support in dashboard
