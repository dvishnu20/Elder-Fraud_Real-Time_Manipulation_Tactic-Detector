# Phase 7 — Privacy and Security Mechanisms

## PII Redaction
The `PrivacyService` implements regex-based redaction to protect sensitive information that victims might disclose during a live phone call.

**Protected Categories:**
- Credit / Debit Card Numbers (16 digits)
- Social Security Numbers (SSN)
- Phone Numbers
- Bank Account Numbers
- Email Addresses
- Authentication / OTP Codes

*Redaction occurs before transcripts are sent to the frontend or logged.*

## Retention Policies
- **Audio Storage**: Explicitly **DISABLED** by default. Audio chunks are processed in memory and immediately discarded.
- **Transcript Storage**: Ephemeral. Only retained in memory for the duration of the active session to provide context in the UI, then discarded upon session termination.

## Limitations
- Regex-based redaction is a best-effort engineering control. It is not a legally comprehensive compliance mechanism.
- Highly novel or obscured PII formats (e.g., spelling out a credit card number in words) may evade regex detection. 
- Further NLP-based Named Entity Recognition (NER) is recommended for production deployment.
