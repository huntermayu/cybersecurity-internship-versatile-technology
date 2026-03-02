# Bug: Input Validation - Rate Limiting Issue

## Target
**salv.com** - FinTech/Compliance Platform

## Description
The message endpoint on contact us form was improperly rate-limited. A potential attacker could post a large number of comments, overloading the server and the notification system. The message endpoint has a speed limit, but the number is set too high - speed limiting activates only when writing more than 1000 comments per minute.

## Affected URL
`https://salv.com/contact/`

## Steps to Reproduce
1. Visit `https://salv.com/contact/`
2. Fill the form and add 1000+ words in message section
3. Copy and paste 5-6 times

## Impact
No rate limit on messages can lead to server slowdown due to large number of contact form submissions.

## Recommendation
Set the speed limit to lower value for endpoints that currently have threshold set too high.

## Status
✅ Fixed - Developers alleviated the problem by reducing the speed limit
