# root-billing-exercise write-up

## Definitions:

1. A payment file is a document that contains info required to move funds from one account to a vendor’s account.
The details included are:
- Payer & Payee info (name, address, emails, and phone numbers)
- Document IDs (invoice number, batch number)
- Key Date (issue date, payment due date)
- Line-item Details (itemized description with quantities, units and individual rates)
- Financial Calculations (subtotals, applied discounts, applicable taxes and total amount due)
- Payment Terms (bank account details, branch or routing codes, currencies, reference numbers)

2. When I refer to ’The Solution’, I mean the Service that produces the billing file and sends it off to the SFTP server

## Assumptions:

- Assuming a bank, through some interface, can securely connect to this solution and provide information about the payee and their bill, the solution can create a billing file that includes some or all of the details of a payment file, and possibly additional details not included in the defined payment file. The banks can send through individual payment files or batch payment files for multiple payees so that many payment files can be sent over with only one transaction.
- The received payee payload (batch or single) is encrypted with TLS when being sent to the solution, and once received, is decrypted with some cryptographic decryption key (symmetric encryption). This data is then used to create a batch or single billing file.
- The billing file adheres to some format for that specific bank and once it is complete, is encrypted (symmetric encryption) and sent to that specific bank’s SFTP server using the TLS protocol.
- The solution and the SFTP server achieve guaranteed delivery with automatic connection retries and resume logic, end-to-end tracking and audit logs, real-time alerts and notifications if a transfer fails, and file integrity validation post-transfer.
- The solution’s job is complete once the billing file(s) is successfuly sent to the bank’s SFTP server free-of-error, and then the bank is responsible for collecting money from their payees. 

## Key AI Suggested Additional Assumptions/Considerations:

- SFTP vs TLS (More accurately: the files might be sent to the bank’s SFTP server through an ecnrypted SSH channel)
- Key Management (Most banks need an additional layer of security/- PGP encryption using the bank’s public key before transfer, and - Azure key vault or some other technology)
- Duplicate Prevention (Unique transaction identifiers and a way to ensure duplicate submissions are detected)
- Bank acknowledgement handling (Mutual TLS authentication between the Solution and the Bank’s SFTP server, and digital signatures, file hashes and signed audit records, and bank acknowledgement as an acceptance criteria for delivery)
- File-level encryption
- Compliance and audit requirements
- Validation and rejection workflows
