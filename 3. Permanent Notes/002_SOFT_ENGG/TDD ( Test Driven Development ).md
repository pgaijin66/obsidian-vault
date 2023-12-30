Type: #Note
Tags: 
# TDD ( Test Driven Development )
Created date: 2023-09-27 21:19


- Writing tests before writing the actual production code
- It follows "Red-Green-Refactor" and focuses on short development iterations

Key concepts of TDD
1. Write tests first
2. Red-Green-Refactor Cycle
	1. Red: Write a test that captures the desired behavior or functionality. Run the test and see it fail.
	2. Green: Write the minimum amount of code needed to make the failing test pass. The focus is on making the test pass quickly, without concerning about the elegance or efficiency of the code.
	3. Refactor: Improve the code's design, remove duplication, make it more maintainable while ensuring that all tests still pass. Refactoring should not change the external behavior of the code.
3. Small iterations: TDD promotes working in a small, incremental steps. Each iteration involves writing a new test or modifying an existing one, followed by writing the code to make the test pass.
4. Test Coverage: TDD aims  for high test coverage, ensuing that every piece of code is tested. This helps to uncover bugs early, provide documentation for the expected behavior and support refactoring.
5. Unit Testing: TDD primarily focuses on unit tests, which are automated tests that verify the the behavior of individual units of code (such as functions or methods) in isolation.
6. Test-Code-Refactor Discipline: Developers following TDD strictly adhere to the discipline of writing a failing test, writing only the necessary code to pass the test, and then refactoring the code.
7. Emergent Design: TDD encourages a design that emerges gradually as the code evolves. Through the refactoring step, the codebase can be continually improved and simplified while maintaining the desired behavior.
8. Fast Feedback Loop: TDD provides fast feedback on changes made to the code. If a change breaks existing functionality, the tests will catch it immediately, allowing for quick identification and resolution of issues.






## References
1. 