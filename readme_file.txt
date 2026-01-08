# 🤖 LinkedIn Job Finder - Automated Resume Matcher

An intelligent n8n workflow that automatically searches LinkedIn for jobs matching your resume, analyzes them with AI, and emails you the top matches in a beautifully formatted table.

![n8n](https://img.shields.io/badge/n8n-automation-orange)
![License](https://img.shields.io/badge/license-MIT-blue)
![OpenAI](https://img.shields.io/badge/OpenAI-GPT--4-green)

## 🎯 Features

- ✅ **AI Resume Analysis** - Automatically extracts your skills, experience level, and desired roles
- ✅ **Smart Job Matching** - Finds the most relevant LinkedIn jobs based on your profile
- ✅ **Beautiful Email Format** - Sends a professional table with job details
- ✅ **Real LinkedIn URLs** - Direct links to apply (no expired/fake jobs!)
- ✅ **Location Filtering** - Search by country, city, or remote
- ✅ **Fresh Jobs Only** - Gets jobs posted in the last 24 hours
- ✅ **Fully Automated** - Set it and forget it!

## 📧 Email Preview

You'll receive emails that look like this:

| Job Title | Company Name | Location | Job Type | Link |
|-----------|--------------|----------|----------|------|
| React Native Developer | CGI | Toronto, ON, Canada | Full-time | [Apply Here](#) |
| Full Stack Developer | Scotiabank | Toronto, ON, Canada | Full-time | [Apply Here](#) |
| Mobile App Developer | Deloitte | Remote (Canada) | Full-time | [Apply Here](#) |

## 🚀 Quick Start

### Prerequisites

You'll need accounts for:
- [n8n](https://n8n.io/) (self-hosted or cloud)
- [Apify](https://apify.com/) (LinkedIn scraping)
- [OpenAI](https://platform.openai.com/) (AI analysis)
- Google account (for Docs and Gmail)

### Installation

1. **Clone this repository**
   ```bash
   git clone https://github.com/YOUR_USERNAME/linkedin-job-finder.git
   cd linkedin-job-finder
   ```

2. **Import workflow into n8n**
   - Open n8n
   - Go to **Workflows → Import from File**
   - Upload `linkedin-job-finder.json`

3. **Set up credentials** (see Configuration section)

4. **Test the workflow**
   - Click "Execute Workflow"
   - Check your email!

## ⚙️ Configuration

### 1. Set Up Credentials in n8n

#### OpenAI API
1. Get your API key from [OpenAI Platform](https://platform.openai.com/api-keys)
2. In n8n: **Credentials → Add Credential → OpenAI**
3. Paste your API key

#### Apify API
1. Get your API token from [Apify Console](https://console.apify.com/account/integrations)
2. In the workflow, replace `YOUR_APIFY_API_KEY_HERE` in the "Scrape LinkedIn Jobs" node

#### Google Services
1. In n8n: **Credentials → Add Credential → Google OAuth2**
2. Connect both Google Docs and Gmail
3. Update credential IDs in the workflow

### 2. Update Your Resume

1. Create a Google Doc with your resume
2. Get the document ID from the URL:
   ```
   https://docs.google.com/document/d/YOUR_DOC_ID_HERE/edit
   ```
3. In the "Get Resume from Google Docs" node, replace `YOUR_GOOGLE_DOC_ID_HERE`

### 3. Configure Job Search

Edit the "Scrape LinkedIn Jobs" node's `jsonBody`:

```json
{
  "count": 100,
  "scrapeCompany": true,
  "urls": [
    "https://www.linkedin.com/jobs/search/?keywords=YOUR_JOB_TITLE&location=YOUR_LOCATION&f_TPR=r86400&sortBy=DD"
  ]
}
```

**Location Examples:**
- `location=Canada` - All of Canada
- `location=Toronto%2C%20Ontario%2C%20Canada` - Toronto
- `location=Canada&f_WT=2` - Remote jobs in Canada

**Keywords:**
- `keywords=full%20stack%20developer`
- `keywords=software%20engineer%20python`
- `keywords=react%20native%20developer`

### 4. Set Your Email

In the "Send Email" node, replace `YOUR_EMAIL@gmail.com` with your email address.

## 📊 Workflow Architecture

```
┌─────────────────┐
│ Manual Trigger  │ (or Schedule Trigger for automation)
└────────┬────────┘
         ↓
┌─────────────────┐
│ Get Resume      │ Fetches resume from Google Docs
└────────┬────────┘
         ↓
┌─────────────────┐
│ Resume Analyzer │ AI extracts skills, experience, titles
└────────┬────────┘
         ↓
┌─────────────────┐
│ Scrape Jobs     │ Gets 100 fresh LinkedIn jobs (Apify)
└────────┬────────┘
         ↓
┌─────────────────┐
│ Combine Jobs    │ Aggregates all jobs into single item
└────────┬────────┘
         ↓
┌─────────────────┐
│ Limit Jobs      │ Reduces to 10 jobs to save tokens
└────────┬────────┘
         ↓
┌─────────────────┐
│ Format Email    │ AI creates HTML email with top matches
└────────┬────────┘
         ↓
┌─────────────────┐
│ Send Email      │ Delivers to your inbox
└─────────────────┘
```

## 🔄 Automation (Schedule)

To run automatically:

1. **Replace Manual Trigger** with **Schedule Trigger**
2. **Configure schedule:**
   - Daily at 9 AM: `0 9 * * *`
   - Monday & Thursday: `0 9 * * 1,4`
   - Twice daily: `0 9,17 * * *`
3. **Activate workflow** (toggle in top-right)

## 💰 Cost Estimate

**Monthly costs (running daily):**
- Apify: ~$20/month (100 jobs/day)
- OpenAI GPT-4: ~$10/month
- n8n: Free (self-hosted) or $20/month (cloud)

**Total: ~$30-50/month**

### Cost Optimization Tips
- Switch to GPT-3.5-turbo (cheaper, change model in AI nodes)
- Reduce job count to 50 instead of 100
- Run every other day instead of daily
- Use n8n self-hosted (free)

## 🐛 Troubleshooting

### "Rate limit exceeded" Error
**Problem:** Too many tokens sent to OpenAI  
**Solution:** In "Limit Jobs" node, change `slice(0, 10)` to `slice(0, 5)`

### No Jobs in Email
**Problem:** Apify scraping failed  
**Solutions:**
- Check Apify credits aren't depleted
- Verify LinkedIn search URL is correct
- Test "Scrape Jobs" node individually

### Email Not Sending
**Problem:** Gmail authentication failed  
**Solutions:**
- Reconnect Gmail OAuth2 credential
- Check spam folder
- Verify email address is correct

### Jobs Are Expired
**Problem:** Old cached results  
**Solution:** URL already includes `f_TPR=r86400` (last 24 hours), but you can try `f_TPR=r43200` (last 12 hours)

## 📝 Customization Ideas

- [ ] Save jobs to Google Sheets for tracking
- [ ] Track which jobs you've already applied to
- [ ] Multiple resume versions for different roles
- [ ] Slack notifications instead of email
- [ ] Filter by salary range
- [ ] Add company ratings/reviews
- [ ] Create weekly summary reports
- [ ] Integration with job application tracker

## 🤝 Contributing

Contributions welcome! Please:
1. Fork the repository
2. Create a feature branch
3. Commit your changes
4. Push and create a Pull Request

## 📄 License

MIT License - feel free to use and modify!

See [LICENSE](LICENSE) file for details.

## ⚠️ Disclaimer

This tool is for **personal use only**. Please:
- Respect LinkedIn's Terms of Service
- Don't scrape excessively (rate limits exist for a reason)
- Use responsibly and ethically

## 🙏 Acknowledgments

Built with:
- [n8n](https://n8n.io/) - Workflow automation platform
- [Apify](https://apify.com/) - LinkedIn job scraping
- [OpenAI](https://openai.com/) - GPT-4 for AI analysis
- [Google Workspace](https://workspace.google.com/) - Docs & Gmail

## 📞 Support

- **Issues:** [GitHub Issues](https://github.com/YOUR_USERNAME/linkedin-job-finder/issues)
- **Discussions:** [GitHub Discussions](https://github.com/YOUR_USERNAME/linkedin-job-finder/discussions)
- **n8n Community:** [n8n Forum](https://community.n8n.io/)

## ⭐ Star This Repo

If this helped you land a job, please star this repo! ⭐

---

Made with ❤️ by Vinayak Dev (https://github.com/vxdcore)
