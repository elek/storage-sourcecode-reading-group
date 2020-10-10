# Storage Source-code Reading Group

> "Show me the code"

## What is it?

An effort to understand better the architecture of existing Open Source **storage** projects (Object Store, Container and (maybe) Block storage), using a method to study source code and use the projects.

Inspired by:

 * [Papers We Love](https://paperswelove.org/) reading groups
 * [The Architecture of Open Source Application](https://aosabook.org/en/index.html) and [@eatonphil](https://twitter.com/phil_eaton)'s suggestion in [HN thread](https://news.ycombinator.com/item?id=24332485)

Writing storage can be hard, but understanding the details of the architecture can be even harder. Papers and docs not always answer all of the questions and some important details can be understood only from source code and with experimenting.

## How does it work?

For each period a storage project is chosen to evaluate. Participants check the source code, start to use it and test it.

Findings are discussed on chat, collected on the wiki, and finally discussed on the *closing* call. In case of too many open questions, the period can be extended.

If you would like to join to the conversation:

 * Connect to the [chat](https://communityinviter.com/apps/stosorego/storage-source-code-reading-group)
 * Check the missing (or existing) sections of the actual wiki page (eg. technical description of read/write path) and contribute more details
 * Run project, measure performance, investigate messages, break it or just us it
 * Call-in to the closing call and share your experience

## Scheduling

 1. Current project: minio,
 2. Closing call: 2020.11.17, 4pm CET. [https://zoom.us/j/97830448292](https://zoom.us/j/97830448292)

Backlog (to suggest new projects, open a PR):

 * [chubaofs](https://github.com/chubaofs/chubaofs)
 * [seaweedfs](https://github.com/chrislusf/seaweedfs)
 * [ambry](https://github.com/linkedin/ambry)
 * [aistore](https://github.com/NVIDIA/aistore)

## Questions to answer

Lot of questions are required to be answered about a storage. For basic understanding we need to understand all the components (network interface / persistence) and basic read / write flow between them.

Answers can be collected on the wiki pages of this github project.

Structure can be changed over the time, but here are some initial questions:

 * Install
   * How to install and run it with the hard way (without scripts / operators, etc.)
   * Required configurations
 * Soure code
   * Used git repositories, related projects, code structure
 * Components
   * what are the required services, and for each service:
     * the persisted storage (format, content)
     * the in-memory state
     * network interfaces (rpc method families, format)
    * main interactions between services
 * Write path
   * What happens when data is written (interactions, RPC messages)
 * Write path
   * What happens when data is read (interactions, RPC messages)
 * Failure scenarios
   * "How to loose data in a safe way?"
   * How to debug problems? Distributed tracing? Important metrics?
 * Performance
   * Base performance numbers with a small cluster
   * Try to repeat published numbers.
 * Used algorithms
   * EC, codecs, ... What are the standard algorith which are used.
 * Community and governance
   * Github activity, bus factor, PR lifetime
   * Governance model
 * References
  * related talks, papers

## How to join?

 * Slack instance: [join](https://communityinviter.com/apps/stosorego/storage-source-code-reading-group)
 * Zoom: https://zoom.us/j/97830448292 check the scheduling section about the closing calls
 * Open pull request to modify this page with any suggestion
