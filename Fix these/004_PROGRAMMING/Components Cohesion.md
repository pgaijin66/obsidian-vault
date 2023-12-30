

- REP: [[Reuse / Release Equivalence Principle]]
		- If you are depending on some component then that dependency must be tracked via release numbers.
		- benefit: engineers will know when release is coming and what to expect in that change. Also helps to determine if the change will break the component that it depends on.
- CCP: [[Common Closure Principle ( SRP for components )]]
		- Components should not have multiple reasons to change
- CRP: [[Common Reuse Principle]]
		- Defined what classes and modules should be places into a components
			- Don't depend on things you don't need
			- Log4J vulnerability
		- Cohesion or coupling