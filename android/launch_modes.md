# Launch Modes in Android

Launch modes allow us to define how a new instance of an activity is associated with the current task. In order to fully understand Launch Modes, we would need to understand two general terms:
  * <b>Tasks</b>: A collection of activities is known as as task. New activities are added to the top of the task.
  * <b>Back Stack</b>: Activities are arranged in the order in which each activity is opened, like a stack. So the first activity that is opened will be the first or the root of the stack. When a new activity is opened, it pushes the activity to the top of the stack. When back is clicked, the topmost activity is popped from the stack.


Now let's look at the four different launch modes in Android:

 * <b>Standard</b>: It creates a new instance of an activity in the task from which it was started. Multiple instances of the activity can be created and multiple instances can be added to the same or different tasks.</br> 
      Eg: Suppose there is an activity stack of <i>A -> B -> C</i>.</br> 
      Now if we launch B again with the launch mode as <b>standard</b>, the new stack will be <i>A -> B -> C -> B</i>.
      
* <b>SingleTop</b>: It is the same as the standard, except if there is a previous instance of the activity that exists in the <b>top of the stack</b>, then it will not create a new instance but rather send the intent to the existing instance of the activity.</br>
      Eg: Suppose there is an activity stack of <i>A -> B</i></br>
      Now if we launch C with the launch mode as <b>singleTop</b>, the new stack will be A -> B -> C as usual.</br>
      Suppose now if there is an activity stack of <i>A -> B -> C</i>. </br> 
      If we launch C again with the launch mode as <b>singleTop</b>, the new stack will still be <i>A -> B -> C</i>
         
* <b>SingleTask</b>: A new task will always be created and a new instance will be pushed to the task as the root one. So if the activity is already in the task, the intent will be redirected to ```onNewIntent()``` else a new instance will be created. At a time only one instance of activity will exist</br>
      Eg: Suppose there is an activity stack of <i>A -> B -> C -> D</i></br>
      Now if we launch D with the launch mode as <b>singleTask</b>, the new stack will be <i>A -> B -> C -> D</i> as usual</br>
      Now if there is an activity stack of <i>A -> B -> C -> D</i>. </br> 
      If we launch activity B again with the launch mode as <b>singleTask</b>, the new activity stack will be <i>A -> B</i>. Activities C and D will be destroyed</i>.
      
      
* <b>SingleInstance</b>: Same as single task but the system does not launch any activities in the same task as this activity. If new activities are launched, they are done so in a separate task.</br>
      Eg: Suppose there is an activity stack of <i>A -> B -> C -> D</i>. If we launch activity B again with the launch mode as <b>singleTask</b>, the new activity stack will be:</i></br>
      Task1:  <i>A -> B -> C</i></br>
      Task2:  <i>D</i>
