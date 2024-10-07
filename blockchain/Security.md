Contract are audited: the audit is a review that focus on security issues and compliancy.

Most common attack
reentrancy (spotted by slither), oracle manipulation (need to be checked manually)

In order to prepare your code for the audit you should make sure that:
- natspec documentation is in place
- code is tested and coverage is good
- be ready to talk with the auditors about implementation details.

Security auditors use static anlysis tools, for solidity the scanner used is **Slither** (by 'Trail of Bits' a popular auditor)
It's a python tool (install with pip install slither), you can run it with 
`slither CONTRACTS_FOLDER`
output is splitted in paragraphs, each one refers to an issues and links to docs. Each paragraph as a different color for the criticality of the issue (green is a warning, red is a critical issue).

Security auditors also uses fuzz testing (automatic testing in a simulated blockchain environment).
You will write the fuzz tests with solidity creating a testing contract (that extends the contract to be tested).

Look for 'trail of bits toolbox' to play with all the security tools that they provide like echidna
**Echidna** is a tool that runs the fuzz tests automatically.

The output of the audit is a report with a list of what they found in the contract. Every issue is associated with a likelyhood to happen and with a criticality of the issue.