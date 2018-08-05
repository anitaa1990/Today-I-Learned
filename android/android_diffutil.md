# DiffUtil in Android RecyclerView

I know I am a little late to the party but this was one of the features that I found quite useful. Let’s say we have a list (the list being populated from the local room database or backend server). We would like to add a new item to the list. Then we sort the list by the date created and update the recyclerView. How would we do this?

One way of doing it would be to add the new item to the list and call ```notifyDataSetChanged()```. But we know calling ```notifyDataSetChanged()```, is a very costly process.

Another way would be to use [DiffUtil](https://developer.android.com/reference/android/support/v7/util/DiffUtil). 

<i>DiffUtil is a utility class that can calculate the difference between two lists and output a list of update operations that converts the first list into the second one. It can be used to calculate updates for a RecyclerView Adapter.</i>

### So how can we use it?
```DiffUtil.Callback``` is an abstract class that is used as a callback when calculating the difference between two lists. It has 4 abstract methods and 1 method non-abstract method.

```getOldListSize()``` – Return the size of the old list.
```getNewListSize()``` – Return the size of the new list.
```areItemsTheSame(int oldItemPosition, int newItemPosition)``` - Called by the DiffUtil to determine whether object number one and object number two represent the same Item. For instance, in our case, if both objects are equal, then the ids of the two objects would be the same.
```areContentsTheSame(int oldItemPosition, int newItemPosition)``` – Checks whether two items have the same data. This method is called by DiffUtil only if areItemsTheSame() method returns true.
```getChangePayload(int oldItemPosition, int newItemPosition)``` – If ```areItemTheSame()``` return <b>true</b> and ```areContentsTheSame()``` returns <b>false</b> DiffUtil calls this method to get a payload about the change.

Example implementation from a [demo](https://github.com/anitaa1990/RoomDb-Sample) app:
```
  public class NoteDiffUtil extends DiffUtil.Callback {

      List<Note> oldNoteList;
      List<Note> newNoteList;
      public NoteDiffUtil(List<Note> oldNoteList, List<Note> newNoteList) {
          this.oldNoteList = oldNoteList;
          this.newNoteList = newNoteList;
      }

      @Override
      public int getOldListSize() {
          return oldNoteList.size();
      }

      @Override
      public int getNewListSize() {
          return newNoteList.size();
      }

      @Override
      public boolean areItemsTheSame(int oldItemPosition, int newItemPosition) {
          return oldNoteList.get(oldItemPosition).getId() == newNoteList.get(newItemPosition).getId();
      }

      @Override
      public boolean areContentsTheSame(int oldItemPosition, int newItemPosition) {
          return oldNoteList.get(oldItemPosition).equals(newNoteList.get(newItemPosition));
      }

      @Nullable
      @Override
      public Object getChangePayload(int oldItemPosition, int newItemPosition) {
          //you can return particular field for changed item.
          return super.getChangePayload(oldItemPosition, newItemPosition);
      }
  }
```

<b>Update method in the adapter class:</b>
```
  public void addTasks(List<Note> newNotes) {
          NoteDiffUtil noteDiffUtil = new NoteDiffUtil(notes, newNotes);
          DiffUtil.DiffResult diffResult = DiffUtil.calculateDiff(noteDiffUtil);
          notes.clear();
          notes.addAll(newNotes);
          diffResult.dispatchUpdatesTo(this);
      }
```

Call ```dispatchUpdatesTo(RecyclerView.Adapter)``` to dispatch the updated list.


<b>Important:</b>
If the lists are large, this operation may take significant time so Google advises us to run the operation in the background task. You can checkout this awesome [article](https://medium.com/@jonfhancock/get-threading-right-with-diffutil-423378e126d2) on how to implement that.
