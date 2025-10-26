# Test Automation Handbook

This test automation handbook is a growing collection of best practices, tips, and showcases demonstrating how test automation is done. It serves both beginners and experts, with the latter finding interesting tips, tricks, patterns, and techniques they should consider during their test development.

Most of the concepts presented are generally independent of the programming language or test framework used, though some test frameworks or languages provide better support for certain approaches.

## Historical Approach to Learning

In my lessons and TA courses, I teach students by following the historical progression of test automation. This is because people very often encounter a technique without understanding that it was used in the past and has mostly been abandoned by the industry due to bad practices, poor maintainability, or other disadvantages. I see this very often with the Record & Play test automation technique.

This approach also leads to a deep understanding of the practices used in test automation and prevents sliding back into historical approaches just because they seem easier, which creates technical debt.

For this purpose, I divide time periods and approaches into different automation levels to easily describe the historical evolution:

- **Level 0** - Mostly plain test scripts where "everything" is mixed together
- **Level 0.5** - Record & Play (you could argue this falls somewhere between levels 0.5-1.5)
- **Level 1** - Start using structures (identifiers grouped together, error handling, result handling... first use of "script" test frameworks such as unit test frameworks, pytest, etc.)
- **Level 2** - Focused deeply on script structure for maintainability and understandability (use of Domain Language, POM - Page Object Model, BDD/DSL/"keyword-driven" test frameworks, Data-Driven test cases, etc.)
- **Level 3** - Focus deeply on structure for better coverage in shorter time (MBT - Model-Based Testing, random order of tests, fuzz tests)
- **Level 4** - (To be defined)
- **Level 5** - (To be defined)

Generally, you can say that any approach, trick, or technique you learn at a certain level can and should be used at higher levels, with additional techniques built on top. Sometimes people jump to a certain level without understanding the benefits of the previous levels' techniques, and this leads to poor implementation and growing technical debt in test automation.