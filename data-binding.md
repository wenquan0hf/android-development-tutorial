# 数据绑定 Data Binding

前面提到 AndroidGraphics2DTutorial 说过它是 ListActivity 派生出来的。ListActivity 中显示的是 ListView，ListView 和 Gallery ，Spinner 有一个共同点：它们都是 AdapterView 的子类。AdapterView 的显示可以通过数据绑定来实现，数据源可以是数组或是数据库记录，数据源和AdapterView 是通过 Adapter 作为桥梁。通过 Adapter，AdatperView 可以显示数据源或处理用户选取时间，如：选择列表中某项。

![](images/19.png)

AndroidGraphics2DTutorial 读取 AndroidManifest.xml中Intent-Filter为

<action android:name=”android.intent.action.MAIN” />
<category android:name=”com.pstreets.graphics2d.SAMPLE_CODE” />

的所有 Activity，以列表方式显示。使用了 Android API 自带的 SimpleAdapter。 来看看AndroidGraphics2DTutorial.java 中相关代码：

```
public class AndroidGraphics2DTutorial extends ListActivity {

 private static final String SAMPLE_CATEGORY
          ="com.pstreets.graphics2d.SAMPLE_CODE";
 
 @Override
 public void onCreate(Bundle savedInstanceState) {
  super.onCreate(savedInstanceState);

  setListAdapter(new SimpleAdapter(this, getData(),
    android.R.layout.simple_list_item_1, new String[] { "title" },
    new int[] { android.R.id.text1 }));
  getListView().setTextFilterEnabled(true);

 }

 protected List getData() {
  List<Map> myData = new ArrayList<Map>();

  Intent mainIntent = new Intent(Intent.ACTION_MAIN, null);
  mainIntent.addCategory(SAMPLE_CATEGORY);

  PackageManager pm = getPackageManager();
  List<ResolveInfo> list = pm.queryIntentActivities(mainIntent, 0);

  if (null == list)
   return myData;

  String[] prefixPath;

  prefixPath = null;

  int len = list.size();

  Map<String, Boolean> entries = new HashMap<String, Boolean>();

  for (int i = 0; i < len; i++) {
   ResolveInfo info = list.get(i);
   CharSequence labelSeq = info.loadLabel(pm);
   String label = labelSeq != null ? labelSeq.toString()
     : info.activityInfo.name;

   String[] labelPath = label.split("/");

   String nextLabel = prefixPath == null ? labelPath[0]
     : labelPath[prefixPath.length];

   if ((prefixPath != null ? prefixPath.length : 0) 
== labelPath.length - 1) {
    addItem(myData,
      nextLabel,
      activityIntent(
        info.activityInfo.applicationInfo.packageName,
        info.activityInfo.name));
   } else {
    if (entries.get(nextLabel) == null) {
     addItem(myData, nextLabel, browseIntent(nextLabel));
     entries.put(nextLabel, true);
    }
   }

  }

  Collections.sort(myData, sDisplayNameComparator);

  return myData;
 }

 private final static Comparator<Map> sDisplayNameComparator 
= new Comparator<Map>() {
  private final Collator collator = Collator.getInstance();

  public int compare(Map map1, Map map2) {
   return collator.compare(map1.get("title"), map2.get("title"));
  }
 };

 protected Intent activityIntent(String pkg, String componentName) {
  Intent result = new Intent();
  result.setClassName(pkg, componentName);
  return result;
 }

 protected Intent browseIntent(String path) {
  Intent result = new Intent();
  result.setClass(this, AndroidGraphics2DTutorial.class);
  return result;
 }

 protected void addItem(List<Map> data, String name, Intent intent) {
  Map<String, Object> temp = new HashMap<String, Object>();
  temp.put("title", name);
  temp.put("intent", intent);
  data.add(temp);
 }

 @Override
 protected void onListItemClick(ListView l, View v, 
    int position, long id) {
  Map map = (Map) l.getItemAtPosition(position);
  Intent intent = (Intent) map.get("intent");
  startActivity(intent);
 }
}
```

使用数据显示 Layout，上面代码中

setListAdapter(new SimpleAdapter(this, getData(),
    android.R.layout.simple_list_item_1, new String[] { “title” },
    new int[] { android.R.id.text1 }));

为ListActivity 中 ListView 指定 Adapter，这个 Adapter 的数据源为 getData()，getData()从Manifest.xml 中查找出所有符合条件的示例 Activity 列表。 这里 DataSource 是静态的从文件中读取，如果 DataSource 为数组或是其它数据源，如果程序中修改数值的内容，则你应该notifyDataSetChanged()来通知 UI 数据有变动。UI则会刷新显示以反映数据变化。简单的说Android 数据绑定和 .Net WinForm ,WPF 中数据绑定类似。

处理用户选取事件，AdapterView.OnItemClickListener()可以用来处理选取事件，对于ListActivity，可以用 protected void onListItemClick(ListView l, View v, int position, long id)。AndroidGraphics2DTutorial中的实现是用户选取 Activity 名称好，则启动对应的 Activity。

上面代码中使用 SimpleAdapter，并使用 Android 提供的android.R.layout.simple_list_item_1来显示数据，Andrid 也允许使用自定义的 Layout 来显示数据，对这个例子来说，可以使用图片加说明来显示列表，将在后面介绍如果使用自定义 Adapter 和自定义 Layout 来显示绑定的数据。

Tags: [Android](http://www.imobilebbs.com/wordpress/archives/tag/android)

