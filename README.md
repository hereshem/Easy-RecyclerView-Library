# Awesome RecyclerView with Easy Intractable Simple or MultiView support and Loadmore Library
*An awesome library for the Android to make simpler with RecyclerView for simple and multi layout view adapter, LoadMore Features, ServerRequests, and many more.*

You can add this library for making RecyclerView more Simpler using the following line in app level ```build.gradle``` file in Android Studio.

```
implementation 'com.hereshem.lib:awesomelib:2.1.0'
```
![](Screenshot_1.png)

## Simple RecyclerView Implementation

**Add RecyclerView in the layout file using the following**

```
<com.hereshem.lib.recycler.MyRecyclerView
        android:id="@+id/recycler"
        app:layoutManager="LinearLayoutManager"
        android:layout_width="match_parent"
        android:layout_height="match_parent"/>
```

**Create View Holders with very easy code**. Provide the type of class that support the holder to display with. That class will be binded to display the content.

```
public static class EVHolder extends MyViewHolder<Events> {
    TextView date, title, summary;
    public EVHolder(View v) {
        super(v);
        date = v.findViewById(R.id.date);
        title = v.findViewById(R.id.title);
        summary = v.findViewById(R.id.summary);
    }
    @Override
    public void bindView(Events c) {
        date.setText(c.date);
        title.setText(c.title);
        summary.setText(c.summary);
    }
}
```
**Create Items List variable and adapters with very few lines**

```
List<Events> items = new ArrayList<>();
MyRecyclerView recycler = findViewById(R.id.recycler);
RecyclerViewAdapter adapter = new RecyclerViewAdapter(this, items, EVHolder.class, R.layout.row_event);
recycler.setAdapter(adapter);
```

**Add ClickListener and LoadMore**

```
recycler.setOnItemClickListener(new MyRecyclerView.OnItemClickListener() {
    @Override
    public void onItemClick(int position) {
        Toast.makeText(MainActivity.this, "Recycler Item Clicked " + position, Toast.LENGTH_SHORT).show();
    }
});

recycler.setOnLoadMoreListener(new MyRecyclerView.OnLoadMoreListener() {
    @Override
    public void onLoadMore() {
        loadData();
    }
});
loadData();
```

**That's it**.

-----------
## Multi Layout RecyclerView 

Create More View Holders

```
public static class TVHolder extends MyViewHolder<String> {
    TextView title;
    public TVHolder(View v) {
        super(v);
        title = v.findViewById(R.id.title);
    }
    @Override
    public void bindView(String c) {
        title.setText(c);
    }
}

public static class DVHolder extends MyViewHolder<Integer> {
    TextView title;
    public DVHolder(View v) {
        super(v);
    }
    @Override
    public void bindView(Integer c) {
    }
}
```

Define Multiview with **MultiLayoutHolder** where the multiple layout is binded to play with the class initialized for the specific ViewHolder with specific layout.

```
List<MultiLayoutHolder> holders = new ArrayList<>();
holders.add(new MultiLayoutHolder(Events.class, EVHolder.class, R.layout.row_event));
holders.add(new MultiLayoutHolder(String.class, TVHolder.class, R.layout.row_simple));
holders.add(new MultiLayoutHolder(Integer.class, DVHolder.class, R.layout.row_divider));

```


**Now Initialize Adapter with very simple code**

```
List<Object> items = new ArrayList<>();
MultiLayoutAdapter adapter = new MultiLayoutAdapter(this, items, holders);
recycler.setAdapter(adapter);
```

The MultiLayout is automatically selected with the type of data added to the items. 

```items.add(new Events(...));``` adds the EHolder and bind the Events item into the RecyclerView.

```items.add(new String("Hem Shrestha"));``` adds the String layout and displays accordingly.

```items.add(new Integer(1));``` selects the divider layout.



----

## Bonus

Online Data request to server made more simpler using the following code.

```
private void loadData() {
    new MyDataQuery(this) {
        @Override
        public void onSuccess(String identifier, String result) {
            List<Events> data = Events.parseJSON(result);
            if (table_name.equals("0")) {
                items.clear();
            }
            if (data.size() > 0) {
                String last="";
                for (int i = 0; i < data.size(); i++) {
                    items.add(new Integer(1)); 					// adding Divider data
                    if (!last.equals(data.get(i).date.substring(5,7))) {
                        last = data.get(i).date.substring(5,7);
                        items.add("For month of " + last); 			// adding String data
                    }
                    items.add(data.get(i)); 					// adding Event data
                }
                recycler.loadComplete();
                start += data.size();
            } else {
                recycler.hideLoadMore();
            }
        }
    }
    .setUrl("http://dl.mantraideas.com/apis/events.json")
    .setMethod(Method.GET)
    .setIdentifier(start+"")
    .execute();
}
```

**Further more** - Offline support can also be provided.

```
new MyDataQuery(this, maps) {
	...
	...
    @Override
    public String onDataQuery(String identifier, MapPair params) {
        if(identifier.equals("0")){
            return new Preferences(getApplicationContext()).getPreferences("data_downloaded");
        }
        return super.onDbQuery(identifier, params);
    }

    @Override
    public void onDataSave(String identifier, String response) {
        if(identifier.equals("0")){
            new Preferences(getApplicationContext()).setPreferences("data_downloaded", response);
        }
    }
    ...
    ...
}...
```

In case of using **Proguard**, use these lines

```
-keep class com.hereshem.lib.** {*;}
-dontwarn com.hereshem.lib.**
```

*Happy Coding :)*