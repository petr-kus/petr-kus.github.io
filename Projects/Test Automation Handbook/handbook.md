# Test Automation Handbook

This test automation handbook is a growing collection of best practices, tips, and showcases demonstrating how test automation is done. It serves both beginners and experts, with the latter finding interesting tips, tricks, patterns, and techniques they should consider during their test development.

Most of the concepts presented are generally independent of the programming language or test framework used, though some test frameworks or languages provide better support for certain approaches.

## Why Another Handbook?

I feel there's still a lack of really good theoretical and practical tips & tricks for QA field, especially for Test Automation, in the online space. Generally, there's a lot of content that is just AI-generated or rewrites "things written in stone." But AI can't figure out something that is largely missing from the internet space. When I'm at test conferences, it's always so refreshing when I talk with experts about test automation and their experiences. So I would like to contribute a little to improving the information that's available on the internet - for me, for you, and for AI - so we don't perpetuate one-sided, shallow content in the future.

## Test Automation Levels: A Historical Progression Approach

In my lessons and TA courses, I teach students by following the historical progression of test automation. This is because people very often encounter a technique without understanding that it was used in the past and has mostly been abandoned by the industry due to bad practices, poor maintainability, or other disadvantages. I see this very often with the Record & Play test automation technique for example.

This level based approach also leads to a deep understanding of the practices used in test automation and prevents sliding back into historical approaches just because they seem easier, which creates technical debt in the end.

For this purpose, I divide time periods and approaches into different automation levels to easily describe the historical evolution:

(automation-levels)=
- **(level-0)=Level 0** - Mostly plain test scripts where "everything" is mixed together
- **(level-0-5)=Level 0.5** - Record & Play (you could argue this falls somewhere between levels 0.5-1.5 depends on the used tool)
- **(level-1)=Level 1** - Start using structures - focus towards "code structure" (identifiers grouped together, error handling, result handling... first use of "script" test frameworks such as unit test frameworks, pytest that makes easier and more general test scripting etc.) → See {ref}`building-foundations`
- **(level-2)=Level 2** - Focused deeply towards "test structure" for better maintainability and understandability (use of Domain Language, POM - Page Object Model, BDD/DSL/"keyword-driven" test frameworks, Data-Driven test cases, etc.) → Related: {ref}`research-warning`
- **(level-3)=Level 3** - Focus towards "test execution" to aim better coverage in shorter time (MBT - Model-Based Testing, random order of tests, property testing, fuzz tests)
- **(level-4)=Level 4** - Focus towards "test generation" (MBT, fuzz test, AI agents, auto generation tests from specification, reuse Data-Driven etc.)
- **(level-5)=Level 5** - (To be defined)

(building-foundations)=
```{tip} 💡 **Building on Foundations**
Generally, you can say that any approach, trick, or technique you learn at a certain level can and should be used at higher levels, with additional techniques built on top. Sometimes people jump to a certain level without understanding the benefits of the previous levels' techniques, or they think they understand them but actually misunderstand them badly. This leads to poor implementation and growing technical debt in test automation.
```

Sometimes even test framework or tool authors are not aware of certain approaches and focus only on solving a specific part of the test automation problem in their solution. This leads to users typically resorting to bad practices or poor workarounds when trying to achieve more comprehensive test automation within the given framework or tool. 

(research-warning)=
```{warning} ⚠️ **Do Your Research First**
What I observe even more frequently is the poor utilization of existing technology (frameworks, tools) by users, test automation testers, or teams, combined with insufficient research to identify appropriate tools or discover existing capabilities within their current toolset. This often leads to reinventing the wheel and developing DIY (do-it-yourself) solutions, ultimately resulting in accumulated technical debt and diminished satisfaction in future QA development efforts.

**Always thoroughly research and read the documentation of your tools before building custom solutions!**
```

## Quick Reference

### Key Concepts
- {ref}`building-foundations` - Why you shouldn't skip automation levels
- {ref}`research-warning` - Always research before building custom solutions

### Automation Levels
- {ref}`level-0` - Plain scripts with everything mixed together
- {ref}`level-1` - Code structure and basic frameworks
- {ref}`level-2` - Test structure and maintainability patterns  
- {ref}`level-3` - Test execution optimization
- {ref}`level-4` - Test generation approaches
- {ref}`level-5` - Future developments

```{seealso}
For practical examples of these concepts in action, explore the detailed guides for each automation level.
``` 