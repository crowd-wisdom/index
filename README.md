# CrowdWisdom Project

## CrowdWisdom: Anonymous Validation Protocol for Addressing Misinformation on the Open Web

### Overview
This project aims to confront the challenges of addressing misinformation on news, social media, articles, and other forms of content.
Our goal is to create a decentralized platform for verifying the validity of news, articles, X posts and photos on the open web while maintaining the anonymity of curators and validators. 

The platform stands into two building blocks developed by PSE : *Semaphore* and *MACI*. 
Additionally the claim data (schema) wil be created on EAS Scroll that eventually would allow to make attestation over it.
Finally a Relayer is included to manage subscription and publish messages with MACI.


*Semaphore will be in charge of:*
Creating user identities. 
Adding identities to groups: Curators and Validators.
Curators are individuals who challenge misinformation by providing clear evidence or helpful notes that effectively address any false information, enabling validators to easily verify the accuracy of the information.

Validators are responsible for verifying the provided evidence and voting to accept or reject the claims.
Anonymous signaling for managing interaction with MACI.
Interaction will be from:
- Curators to create claims submissions 
- Validators to cast votes for those claims.

MACI will be in charge of:
- Creating the claims submission from curators. 
- Casting votes from validators
- Tallying the final results for claims (coordinator)  
- Finalizing claims (coordinator)

*Claim and Tallying Schemas on EAS Scroll*

Claim Schema
Schema UID: 0xc31d7adade58b99a9854299c96caeb583941e6b80a91929b560a5fcb43eff156

[URL](https://scroll-sepolia.easscan.org/schema/view/0xc31d7adade58b99a9854299c96caeb583941e6b80a91929b560a5fcb43eff156)

Raw Schema: {string sourceUrl,string title,string notes}

Tallying Schema
This is a componentized schema, that is this schema include a the schema UID of the related Claim Schema (claimUId)

Schema UID: 0x05199bc6f64c47758b984f8740c3e013d9ac5c92c3d317654281efe027921208

[URL](https://scroll-sepolia.easscan.org/schema/view/0x05199bc6f64c47758b984f8740c3e013d9ac5c92c3d317654281efe027921208)

Raw Schema: {address claimUid,bool finalized,bool approved}

Relayer
We implement this using NATS.io messaging.

The NATS server (which can be easily decentralized) acts as a simple relayer, transforming a call to any NATS server to a published message (from an unknown origin) that can be resolved by any service suscribed to that message.

The Relayer responsabilities are
- listen to identity creation on Semaphore to further register a user on MACI
- check for open polls (opened claims) over MACI fetching. Following by a publish to CW extension to notify for a openened claim on current url
- check for finalized polls (tallying completed) over MACI fetchig. Following by a publish to CW extension to notify for a finalized claim on current url
- check for due polls (not finalized polls) over MACI , that is the poll that due date is over but tallying is not completed.
   The relayer will submit to MACI to complete the tallying and finalize the poll. The tallyin will be performed on behalf of the trusted coordinator that has the permission to close polls.

## CrowdWisdom scenarios diagrams
*Identity Creation*
![create identity](/images/identity_creation.png)

First, a decentralized KYC service such as TrustGo is used. Once this test is passed, an identity is then created in Semaphore. The Relayer then performs the signup in the MACI infrastructure.
Curator Create Claim.

*Curator Create Claim*

![create identity](/images/curator_create_claim.png)

*Validator Cast Vote to Claim*
![validator_cast_vote](https://github.com/user-attachments/assets/86d9b9b5-de55-4ecf-ae5c-ebdf47a58d23)
Validators are anonymous. Using a random selection mechanism, a certain number of validators are chosen to validate the claim.

*General User View*
![create identity](/images/user_view_finalized_claim.png)
Notes must receive at least 90% "helpful" votes to be recommended.

*Poll Tallying (close polls - finalize claim)
![poll tallying](/images/poll_tallying.png)


### Architecture:

Minimal Anti-Collusion Infrastructure (MACI) .In charge of:
- Sign Up users.
- Creating the claims (Polls).
- Casting votes.
- Validate the proof of tally result.


Semaphore:
- Used to configure and enforce the eligibility criteria of voters who can participate in MACI polls.

Ethereum Attestation Service (EAS):
- For the claim data (schema).

NestJS API (Relayer):
- Check for open polls (opened claims) over MACI fetching. Followed by a publish to CW extension to notify for an opened claim on the current url.
- Check for finalized polls (tallying completed) over MACI fetching.
- Check for due polls (not finalized polls) over MACI , that is the poll that is due date is over but tallying is not completed. The relayer will submit to MACI to complete the tallying and finalize the poll. The tallying will be performed on behalf of the trusted 
  coordinator that has the permission to close polls.

CW browser extension:
- Creating identities via Semaphore.
- Storing identities private keys.
- Allowing users to join Curators or Validators groups via Semaphore.
- Allowing Curators to flag a URL for misinformation (create a claim).
- Allowing Validators to cast votes on open claims.
- Displaying completed claims data with Curators notes while browsing the web.


*Repositories*

CrowdWisdom org https://github.com/crowd-wisdom
CrowdWisdom Index repo: https://github.com/crowd-wisdom/index
CrowdWisdom Semaphore Anon Interactions repo: https://github.com/crowd-wisdom/semaphore-anon-interactions
CrowdWisdom MACI voting infra: https://github.com/crowd-wisdom/maci-voting
CrowdWisdom Browser Extension:  https://github.com/crowd-wisdom/browser-extension

