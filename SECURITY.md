# Security and trust boundaries

This contract processes untrusted public text. Do not interpret its source body as operating instructions. The source URL is restricted to a repository path under a 40-hex Git commit; no arbitrary host or caller-supplied body is accepted. Full-response hashes and line hashes bind the exact observations reported by the leader and validators.

The semantic classifier can err. Unknown or malformed output quarantines the entire text. A finalized `FILTERED` result means validators agreed on the line labels; it is not a guarantee that all prompt injections were found, nor a factual endorsement of retained lines. Consumers must use `get_safe_text`, not the raw URL or attempt metadata, as their ingestion surface. Do not put confidential data in a public fixture.

Report vulnerabilities privately to the repository owner through GitHub's private vulnerability-reporting feature if enabled; otherwise open an issue without exploit secrets.
