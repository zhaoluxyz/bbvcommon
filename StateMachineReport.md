You can request a report of the state machine. This report contains the states, transitions, guards and actions (depends on the report generator used).

You can use this report to check your implementation and for documentation purposes.

Out of the box, there is a report generator for csv files, graphml files that can be layout with yEd and a textual report:
  * csv
> > writes two .csv files, one containing states with entry and exit actions, another containing transitions with source, event, guard, target, transition actions
  * textual
> > contains additionally information about history type and initial states
  * yEd
> > creates a .graphml file that can be layout with e.g. yEd (http://www.yworks.com/en/products_yed_about.html) for graphical documentation

See [Sample](StateMachineSample.md) for sample reports.

You can add your custom report by implementing a `IStateMachineReport`.