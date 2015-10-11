<h1>
<blockquote>bbv.Common.StateMachine.Specification<br>
</h1>
<h2>
</blockquote><blockquote>5 concerns,<br>
28 contexts,<br>
66 specifications<br>
</h2></blockquote>

> 

&lt;hr/&gt;


> 

&lt;hr/&gt;


<h2>
<blockquote>Entry and exit actions specifications<br>
</h2>
<h3>
8 contexts, 12 specifications<br>
</h3><h3>
When an entry action of several entry actions throws an exception<br>
</h3>
<h4>
2 specifications<br>
</h4><ul><li><a>should execute all entry actions on entry</a></blockquote>







<blockquote></li><li><a>should handle all exceptions of all throwing entry actions</a></blockquote>







<blockquote></li>
</blockquote><blockquote></ul><h3>
</blockquote><blockquote>When an exit action of several exit actions throws an exception<br>
</h3>
<h4>
2 specifications<br>
</h4><ul><li><a>should execute all exit actions on exit</a></blockquote>







<blockquote></li><li><a>should handle all exceptions of all throwing exit actions</a></blockquote>







<blockquote></li>
</blockquote><blockquote></ul><h3>
</blockquote><blockquote>When defining an entry action<br>
</h3>
<h4>
</blockquote><ol><li>specification<br>
</h4><ul><li><a>should execute entry action on entry</a></li></ol>







<blockquote></li>
</blockquote><blockquote></ul><h3>
</blockquote><blockquote>When defining an entry action with parameter<br>
</h3>
<h4>
2 specifications<br>
</h4><ul><li><a>should execute entry action on entry</a></blockquote>







<blockquote></li><li><a>should pass parameter to entry action</a></blockquote>







<blockquote></li>
</blockquote><blockquote></ul><h3>
</blockquote><blockquote>When defining an exit action<br>
</h3>
<h4>
</blockquote><ol><li>specification<br>
</h4><ul><li><a>should execute exit action on exit</a></li></ol>







<blockquote></li>
</blockquote><blockquote></ul><h3>
</blockquote><blockquote>When defining an exit action with parameter<br>
</h3>
<h4>
2 specifications<br>
</h4><ul><li><a>should execute exit action on exit</a></blockquote>







<blockquote></li><li><a>should pass parameter to exit action</a></blockquote>







<blockquote></li>
</blockquote><blockquote></ul><h3>
</blockquote><blockquote>When defining multiple entry actions<br>
</h3>
<h4>
</blockquote><ol><li>specification<br>
</h4><ul><li><a>should execute all entry actions on entry</a></li></ol>







<blockquote></li>
</blockquote><blockquote></ul><h3>
</blockquote><blockquote>When defining multiple exit actions<br>
</h3>
<h4>
</blockquote><ol><li>specification<br>
</h4><ul><li><a>should execute all exit actions on exit</a></li></ol>







<blockquote></li>
</blockquote><blockquote></ul><h2>
</blockquote><blockquote>Exception Handling specifications<br>
</h2>
<h3>
7 contexts, 28 specifications<br>
</h3><h3>
When a guard in a transition throws an exception<br>
</h3>
<h4>
6 specifications<br>
</h4><ul><li><a>should catch exception and fire transition exception event</a></blockquote>







<blockquote></li><li><a>should pass source state of failing transition to event arguments of transition exception event</a></blockquote>







<blockquote></li><li><a>should pass event id causing transition to event arguments of transition exception event</a></blockquote>







<blockquote></li><li><a>should pass thrown exception to event arguments of transition exception event</a></blockquote>







<blockquote></li><li><a>should pass event parameters to event arguments of transition exception event</a></blockquote>







<blockquote></li><li><a>should not fire exception thrown event</a></blockquote>







<blockquote></li>
</blockquote><blockquote></ul><h3>
</blockquote><blockquote>When a transition action throws an exception<br>
</h3>
<h4>
6 specifications<br>
</h4><ul><li><a>should catch exception and fire transition exception event</a></blockquote>







<blockquote></li><li><a>should pass source state of failing transition to event arguments of transition exception event</a></blockquote>







<blockquote></li><li><a>should pass event id causing transition to event arguments of transition exception event</a></blockquote>







<blockquote></li><li><a>should pass thrown exception to event arguments of transition exception event</a></blockquote>







<blockquote></li><li><a>should pass event parameters to event arguments of transition exception event</a></blockquote>







<blockquote></li><li><a>should not fire exception thrown event</a></blockquote>







<blockquote></li>
</blockquote><blockquote></ul><h3>
</blockquote><blockquote>When an entry action in a transition throws an exception<br>
</h3>
<h4>
6 specifications<br>
</h4><ul><li><a>should catch exception and fire transition exception event</a></blockquote>







<blockquote></li><li><a>should pass source state of failing transition to event arguments of transition exception event</a></blockquote>







<blockquote></li><li><a>should pass event id causing transition to event arguments of transition exception event</a></blockquote>







<blockquote></li><li><a>should pass thrown exception to event arguments of transition exception event</a></blockquote>







<blockquote></li><li><a>should pass event parameters to event arguments of transition exception event</a></blockquote>







<blockquote></li><li><a>should not fire exception thrown event</a></blockquote>







<blockquote></li>
</blockquote><blockquote></ul><h3>
</blockquote><blockquote>When an exit action in a transition throws an exception<br>
</h3>
<h4>
6 specifications<br>
</h4><ul><li><a>should catch exception and fire transition exception event</a></blockquote>







<blockquote></li><li><a>should pass source state of failing transition to event arguments of transition exception event</a></blockquote>







<blockquote></li><li><a>should pass event id causing transition to event arguments of transition exception event</a></blockquote>







<blockquote></li><li><a>should pass thrown exception to event arguments of transition exception event</a></blockquote>







<blockquote></li><li><a>should pass event parameters to event arguments of transition exception event</a></blockquote>







<blockquote></li><li><a>should not fire exception thrown event</a></blockquote>







<blockquote></li>
</blockquote><blockquote></ul><h3>
</blockquote><blockquote>When exception is thrown and no exception even handler is registered<br>
</h3>
<h4>
</blockquote><ol><li>specification<br>
</h4><ul><li><a>should throw exception</a></li></ol>







<blockquote></li>
</blockquote><blockquote></ul><h3>
</blockquote><blockquote>When initializing the state machine and an entry action throws an exception<br>
</h3>
<h4>
2 specifications<br>
</h4><ul><li><a>should fire exception throw event</a></blockquote>







<blockquote></li><li><a>should pass thrown exception to event arguments of exception thrown event</a></blockquote>







<blockquote></li>
</blockquote><blockquote></ul><h3>
</blockquote><blockquote>When transition throws exception and no transition exception even handler is registered<br>
</h3>
<h4>
</blockquote><ol><li>specification<br>
</h4><ul><li><a>should throw exception</a></li></ol>







<blockquote></li>
</blockquote><blockquote></ul><h2>
</blockquote><blockquote>Execute transition specifications<br>
</h2>
<h3>
5 contexts, 13 specifications<br>
</h3><h3>
When firing an event onto a started hierarchical state machine with source and destination having a common ancestor<br>
</h3>
<h4>
</blockquote><ol><li>specification<br>
</h4><ul><li><a>should remain inside common ancestor state</a></li></ol>







<blockquote></li>
</blockquote><blockquote></ul><h3>
</blockquote><blockquote>When firing an event onto a started hierarchical state machine with source and destination having different parents<br>
</h3>
<h4>
5 specifications<br>
</h4><ul><li><a>should execute exit action of source state</a></blockquote>







<blockquote></li><li><a>should execute exit action of parent of source state</a></blockquote>







<blockquote></li><li><a>should execute entry action of parent of destination state</a></blockquote>







<blockquote></li><li><a>should execute entry action of destination state</a></blockquote>







<blockquote></li><li><a>should execute actions from source upwards and then downwards to destination state</a></blockquote>







<blockquote></li>
</blockquote><blockquote></ul><h3>
</blockquote><blockquote>When firing an event onto a started state machine<br>
</h3>
<h4>
5 specifications<br>
</h4><ul><li><a>should execute transition by switching state</a></blockquote>







<blockquote></li><li><a>should execute transition actions</a></blockquote>







<blockquote></li><li><a>should pass parameters to transition action</a></blockquote>







<blockquote></li><li><a>should execute exit action of source state</a></blockquote>







<blockquote></li><li><a>should execute entry action of destination state</a></blockquote>







<blockquote></li>
</blockquote><blockquote></ul><h3>
</blockquote><blockquote>When firing an event onto a started state machine with guarded transitions<br>
</h3>
<h4>
</blockquote><ol><li>specification<br>
</h4><ul><li><a>should take transition guarded with first matching guard</a></li></ol>







<blockquote></li>
</blockquote><blockquote></ul><h3>
</blockquote><blockquote>When firing an event onto a started state machine with otherwise guard and no matching guard<br>
</h3>
<h4>
</blockquote><ol><li>specification<br>
</h4><ul><li><a>should take transition guarded with otherwise</a></li></ol>







<blockquote></li>
</blockquote><blockquote></ul><h2>
</blockquote><blockquote>Initialize state machine specifications<br>
</h2>
<h3>
6 contexts, 11 specifications<br>
</h3><h3>
When an already initialized state machine is initialized<br>
</h3>
<h4>
</blockquote><ol><li>specification<br>
</h4><ul><li><a>should throw an invalid operation exception</a></li></ol>







<blockquote></li>
</blockquote><blockquote></ul><h3>
</blockquote><blockquote>When an initialized state machine is started<br>
</h3>
<h4>
2 specifications<br>
</h4><ul><li><a>should set current state of state machine to state to which it is initialized</a></blockquote>







<blockquote></li><li><a>should execute entry action of state to which state machine is initialized</a></blockquote>







<blockquote></li>
</blockquote><blockquote></ul><h3>
</blockquote><blockquote>When an state machine is initialized<br>
</h3>
<h4>
</blockquote><ol><li>specification<br>
</h4><ul><li><a>should not yet execute any entry actions</a></li></ol>







<blockquote></li>
</blockquote><blockquote></ul><h3>
</blockquote><blockquote>When an uninitialized state machine is started<br>
</h3>
<h4>
</blockquote><ol><li>specification<br>
</h4><ul><li><a>should throw an invalid operation exception</a></li></ol>







<blockquote></li>
</blockquote><blockquote></ul><h3>
</blockquote><blockquote>When initializing to a super state of a hierarchical state machine<br>
</h3>
<h4>
3 specifications<br>
</h4><ul><li><a>should set current state of state machine to initial leaf state of the state to which it is initialized</a></blockquote>







<blockquote></li><li><a>should execute entry action of super state to which state machine is initialized</a></blockquote>







<blockquote></li><li><a>should execute entry actions of initial sub states until a leaf state is reached</a></blockquote>







<blockquote></li>
</blockquote><blockquote></ul><h3>
</blockquote><blockquote>When initializing to leaf state of a hierarchical state machine<br>
</h3>
<h4>
3 specifications<br>
</h4><ul><li><a>should set current state of state machine to state to which it is initialized</a></blockquote>







<blockquote></li><li><a>should execute entry action of state to which state machine is initialized</a></blockquote>







<blockquote></li><li><a>should execute entry action of super states of the state to which state machine is initialized</a></blockquote>







<blockquote></li>
</blockquote><blockquote></ul><h2>
</blockquote><blockquote>Start and stop state machine specifications<br>
</h2>
<h3>
2 contexts, 2 specifications<br>
</h3><h3>
When starting an initialized state machine<br>
</h3>
<h4>
</blockquote><ol><li>specification<br>
</h4><ul><li><a>should execute queued events</a></li></ol>







<blockquote></li>
</blockquote><blockquote></ul><h3>
</blockquote><blockquote>When stopping a running state machine<br>
</h3>
<h4>
</blockquote><ol><li>specification<br>
</h4><ul><li><a>should queue events</a></li></ol>







<blockquote></li>
</blockquote><blockquote></ul>
</blockquote>> 

&lt;hr/&gt;

