# CrowdWisdom Project

## CrowdWisdom: Anonymous Validation Protocol for Addressing Misinformation on the Open Web

### Overview
This project aims to confront the challenges of addressing misinformation on news, social media, articles, and other forms of content.
Our goal is to create a decentralized platform for verifying the validity of news, articles, X posts and photos on the open web while maintaining the anonymity of curators and validators. 

The platform stands into two building blocks developed by PSE : *Semaphore* and *MACI*. 
Additionally the claim data (schema) wil be created on EAS Scroll that eventually would allow to make attestation over it.
Finally a Relayer is included to manage subscription and publish messages with Maci.


*Semaphore will be in charge of:*
Creating user identities. 
Adding identities to groups: Curators and Validators.
Curators are individuals who challenge misinformation by providing clear evidence or helpful notes that effectively address any false information, enabling validators to easily verify the accuracy of the information.

Validators are responsible for verifying the provided evidence and voting to accept or reject the claims.
Anonymous signaling for managing interaction with MACI.
Interaction will be from:
- curators to create claims submissions 
- validators to cast votes for those claims.

MACI will be in charge of:
- Creating the claims submission from curators. 
- Casting votes from validators
- Tallying the final results for claims (coordinator)  
- Finalizing claims (coordinator)

*Claim Data Schema on EAS Scroll*

Schema UID: 0x89b0b48cba2a362eebc656ec1a7fa92ee0a952dc985e3f5c63faceaa2bbfcd8d

Url: 
https://scroll-sepolia.easscan.org/schema/view/0x89b0b48cba2a362eebc656ec1a7fa92ee0a952dc985e3f5c63faceaa2bbfcd8d

Raw Schema:

{string sourceUrl, string title, string notes, bool finalized, bool approved}

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

*Curator Create Claim*
![create identity](/images/curator_create_claim.png)

*Validator Cast Vote to Claim*
![create identity](/images/validator_cast_vote.png)

*General User View*
![create identity](/images/user_view_finalized_claim.png)

*Poll Tallying (close polls - finalize claim)
![poll tallying](/images/poll_tallying.png)

*Browser Extension* 

We implemented a browser extension that will be in charge of:

- Creating identities via Semaphore.
- Storing identities' private keys.
- Allowing users to join Curators or Validators groups via Semaphore.
- Allowing Curators to flag a URL for misinformation (create a claim).
- Allowing Validators to cast votes on open claims.
- Displaying completed claims data with Curators' notes while browsing the web.

*Repositories*

CrowdWisdom org https://github.com/crowd-wisdom
CrowdWisdom Index repo: https://github.com/crowd-wisdom/index
CrowdWisdom Semaphore Anon Interactions repo: https://github.com/crowd-wisdom/semaphore-anon-interactions
CrowdWisdom MACI voting infra: https://github.com/crowd-wisdom/maci-voting
CrowdWisdom Browser Extension:  https://github.com/crowd-wisdom/browser-extension

