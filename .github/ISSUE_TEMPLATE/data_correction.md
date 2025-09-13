---
name: Data Correction
about: Report incorrect or missing data in the database
title: "[DATA] "
labels: data
assignees: ''

---

## 📊 Data Issue Type
- [ ] Incorrect artist information
- [ ] Missing artist or performance
- [ ] Wrong event date/venue
- [ ] Duplicate entries
- [ ] Other data inconsistency

## 🎵 Artist/Event Details
**Artist Name:** 
**Event Date:** 
**Venue:** Berghain / Panorama Bar
**API Response (if applicable):**

## ❌ Current (Incorrect) Data
What does the database currently show?

```json
{
  "current_data": "paste API response or describe what you see"
}
```

## ✅ Correct Data
What should it show instead?

## 📚 Source/Evidence
Please provide evidence for the correction:
- [ ] Official Berghain/Resident Advisor listing
- [ ] Historical flyer or promotional material
- [ ] Personal attendance/knowledge
- [ ] Other reliable source

**Source URL/Description:**

## 📋 Additional Context
- How did you discover this issue?
- Any other related data that might need correction?
- Historical context about the event/artist

## 🔍 Search/API Query Used
How did you find this data? (helps us understand if there are systematic issues)

```http
GET /api/endpoint?parameters
```
