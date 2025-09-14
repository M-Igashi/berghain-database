# 🤝 Contributing to Berghain Database

Thank you for your interest in contributing to the Berghain Database project! This project is maintained by the Techno community, for the community.

## 🎯 How You Can Help

### 1. 🐛 Report Bugs
Found something that doesn't work? Let us know!

- Use the [Bug Report template](https://github.com/M-Igashi/berghain-database/issues/new?template=bug_report.md)
- Include API responses when relevant
- Describe steps to reproduce the issue

### 2. 📊 Report Data Issues
Help us keep the database accurate:

- Use the [Data Correction template](https://github.com/M-Igashi/berghain-database/issues/new?template=data_correction.md)
- Provide evidence for corrections (official sources, historical flyers, etc.)
- Include source information and verification details

### 3. ✨ Suggest Features
Have an idea for improvement?

- Use the [Feature Request template](https://github.com/M-Igashi/berghain-database/issues/new?template=feature_request.md)
- Explain the use case and value to the community
- Consider API design implications

### 4. 📚 Improve Documentation
- Fix typos or unclear explanations
- Add practical usage examples
- Improve API documentation clarity

## 🎵 Data Contribution Guidelines

### What Data We Accept

**✅ Good Contributions:**
- Historical Berghain/Panorama Bar performances (**November 2009 - September 2025**)
- Corrections with reliable, verifiable sources
- Missing artist performance records
- Event details from official Berghain sources
- Artist name normalization improvements (handling special characters: ¥, Ø, ø, À-ÿ)

**❌ What We Don't Accept:**
- Data from other venues or clubs
- Events outside our documentation period (pre-2009 or non-Berghain events)
- Speculative or unverified information
- Personal opinions presented as factual data
- Commercially sensitive or private information

### Source Requirements

All data corrections must include **credible, verifiable sources**:

1. **Primary Sources (Best):**
   - Official Berghain/Ostgut Ton announcements
   - Original event flyers and promotional materials
   - Berghain's official website archives
   - Resident Advisor event listings (contemporary)
   
2. **Secondary Sources (Good):**
   - Contemporary Techno magazine articles (from the event period)
   - Verified historical documentation
   - Official artist discographies mentioning Berghain performances
   - Music press coverage from the time

3. **Community Sources (Case-by-case):**
   - Personal attendance with specific, verifiable details
   - Multiple community corroborations
   - DJ/artist social media posts from the performance period

## 📝 Issue Guidelines

### Before Creating an Issue

1. **Search existing issues** to avoid duplicates
2. **Test the API** - verify it's actually a bug by testing endpoints
3. **Gather information** - API responses, error messages, timestamps
4. **Check multiple examples** - ensure it's not an isolated case

### Writing Good Issues

**Be Specific:**
- ❌ "Search doesn't work"
- ✅ "Artist search returns 500 error for queries containing special characters like 'Rødhåd'"

**Include Technical Details:**
- API endpoint used (e.g., `/api/artists?search=ben+klock`)
- Full response or error message
- Browser/client information if relevant
- Timestamp when the issue occurred

**Provide Context:**
- What were you trying to achieve?
- What did you expect to happen?
- What actually happened?

**Show Evidence:**
- Screenshots for UI issues
- JSON responses for API issues
- curl commands that reproduce the issue

## 🚀 API Usage Best Practices

### For Developers Using the API

**Respectful Usage:**
- **Cache responses** appropriately (our cache TTL: 2-24 hours depending on endpoint)
- **Handle rate limits** gracefully (we have generous limits but fair use applies)
- **Use appropriate endpoints** - `/api/artists/ranking` for rankings, `/api/artists` for search
- **Implement proper error handling** for network timeouts and API errors

**Performance Optimization:**
- Use `limit` parameters to control response size
- Implement pagination for large datasets
- Consider using `/api/artists/by-name/{name}` for exact matches instead of search

**Technical Integration:**
- **CORS is enabled** for web applications
- **Response times** average <100ms for cached responses
- **Search normalization** handles special characters automatically

### Data Access Ethics

- **Respect the data** - this represents real artists and cultural history spanning 16+ years
- **Credit the source** - mention "Berghain Database" in your projects using this data
- **Share improvements** - if you find errors or inconsistencies, report them back
- **Non-commercial use** - respect the community nature of this project
- **Preserve context** - when using data, maintain the cultural significance of Berghain

## 🔍 Common Data Issues to Look For

### Artist Name Issues
- **Special character normalization** - Artists like "Rødhåd", "µ-Ziq", "Atom™"
- **Spelling variations** - Different spellings across events
- **Missing performances** - Artists you know performed but aren't in the database

### Event Data Issues
- **Date inconsistencies** - Wrong dates or formatting issues
- **Missing events** - Klubnacht sessions not recorded
- **URL problems** - Broken or incorrect Berghain event URLs
- **Artist count mismatches** - `total_artists` vs `actual_performances` discrepancies

### Performance Record Issues
- **Venue misattribution** - Artists listed for wrong floor (Berghain vs Panorama Bar)
- **Duplicate entries** - Same performance recorded multiple times
- **Missing performances** - Known performances not in database

## 🏷️ Issue Labels

We use these labels to organize issues:

- `bug` - Something isn't working as expected
- `enhancement` - New feature or improvement request
- `data` - Data correction, addition, or quality issue
- `documentation` - Documentation improvements or clarifications
- `api` - API-specific issues or improvements
- `search` - Search functionality issues
- `performance` - Performance or optimization concerns
- `good first issue` - Easy for newcomers to tackle
- `help wanted` - Looking for community contributors
- `duplicate` - Issue already exists
- `wontfix` - Won't be implemented (with explanation)

## ⚡ Response Time Expectations

- **Bug reports**: Acknowledgment within 48 hours, investigation begins
- **Data corrections**: May take longer due to source verification requirements
- **Feature requests**: Reviewed during periodic planning sessions
- **Documentation issues**: Usually addressed within a week

## 🎵 Community Standards

This project celebrates Techno culture and preserves the legacy of one of the world's most important nightclub venues. We expect:

- **Respect** for all contributors, artists, and community members
- **Accuracy** in data contributions with proper source verification
- **Constructive** feedback and discussions about improvements
- **Patience** as this is maintained by volunteers in their free time
- **Cultural sensitivity** to the significance of Berghain in electronic music history

## 🔧 Testing Your Contributions

### API Testing
Before reporting bugs, test with multiple examples:

```bash
# Test basic artist search
curl "https://berghain.ravers.workers.dev/api/artists?search=ben+klock"

# Test special characters
curl "https://berghain.ravers.workers.dev/api/artists?search=rødhåd"

# Test specific artist details
curl "https://berghain.ravers.workers.dev/api/artists/16"

# Test performance history
curl "https://berghain.ravers.workers.dev/api/artists/16/performances"
```

### Data Verification
When suggesting data corrections:
1. **Cross-reference** multiple sources when possible
2. **Provide URLs** to verifiable sources
3. **Include dates** and specific event details
4. **Note discrepancies** between your sources and current database

## 🙏 Recognition

Contributors will be recognized:
- In release notes for significant data contributions
- In the project documentation for ongoing contributors
- With our eternal gratitude for preserving 16+ years of Techno history!

Major contributors may be invited to help with database moderation and quality control.

## 📞 Questions?

Not sure about something?
- Create a [general question issue](https://github.com/M-Igashi/berghain-database/issues/new)
- Check existing issues and discussions first
- Review the main README.md for comprehensive API documentation
- Remember: thoughtful questions help improve the project for everyone!

## 🌟 Special Recognition

This database represents one of the most comprehensive archives of a single venue's history in electronic music. Contributors help preserve:

- **16+ years** of continuous documentation
- **2,070+ artists** from the global Techno community  
- **10,137+ performance records** from the world's most legendary Techno venue
- The cultural legacy of **Berghain's golden era** and its ongoing influence

---

**Thank you for helping preserve and share the legacy of Berghain and the global Techno community!** 🎵

*Built with ❤️ for ravers, by ravers*