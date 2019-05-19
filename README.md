# 期中实验：NotePad功能扩展
一、实验内容
============  
1、基本要求  
--------------  
（1）NoteList中显示条目增加时间戳显示  

（2）添加笔记查询功能（根据标题查询）  
  
2、附加功能  
--------------  
（1）UI美化：更改主题颜色，美化UI界面；  

（2）背景更换：更改笔记背景颜色；  

（3）导出笔记：将笔记导出到手机存储，保存为txt文件；  

（4）笔记排序：按创建时间排序、按修改时间排序、按笔记背景颜色排序。  

二、关键代码与截图  
==================  
1、显示条目增加时间戳显示  
------------------  
在noteslist_item.xml布局文件中添加显示时间的TextView  
```  
 <TextView
            android:id="@+id/text2"
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:textAppearance="?android:attr/textAppearanceLarge"
            android:textSize="20sp"/>  
```  
在NoteList.java的PROJECTION中定义显示的时间  
```  
 private static final String[] PROJECTION = new String[] {
            NotePad.Notes._ID, // 0
            NotePad.Notes.COLUMN_NAME_TITLE, // 1
            NotePad.Notes.COLUMN_NAME_MODIFICATION_DATE,//加入修改时间的显示
            NotePad.Notes.COLUMN_NAME_BACK_COLOR,
    };  
```  
在dataColumns，viewIDs中补充时间部分  
```  
String[] dataColumns = { NotePad.Notes.COLUMN_NAME_TITLE ,  NotePad.Notes.COLUMN_NAME_MODIFICATION_DATE } ;
int[] viewIDs = { android.R.id.text1 , R.id.text2 };  
```  
在NotePadProvider中的insert方法和NoteEditor中的updateNote方法中，把时间戳改为以时间格式存入  
```  
 // Gets the current system time in milliseconds
        Long now = Long.valueOf(System.currentTimeMillis());
        Date date = new Date(now);
        SimpleDateFormat format = new SimpleDateFormat("yyyy-MM-dd HH:mm:ss");
        String dateTime = format.format(date);

        // If the values map doesn't contain the creation date, sets the value to the current time.
        if (values.containsKey(NotePad.Notes.COLUMN_NAME_CREATE_DATE) == false) {
            values.put(NotePad.Notes.COLUMN_NAME_CREATE_DATE, dateTime);
        }

        // If the values map doesn't contain the modification date, sets the value to the current
        // time.
        if (values.containsKey(NotePad.Notes.COLUMN_NAME_MODIFICATION_DATE) == false) {
            values.put(NotePad.Notes.COLUMN_NAME_MODIFICATION_DATE, dateTime);
        }  
```  
```  
    private final void updateNote(String text, String title) {
        Long now = Long.valueOf(System.currentTimeMillis());
        Date date = new Date(now);
        SimpleDateFormat format = new SimpleDateFormat("yyyy-MM-dd HH:mm:ss");
        String dateTime = format.format(date);
        ContentValues values = new ContentValues();
        values.put(NotePad.Notes.COLUMN_NAME_MODIFICATION_DATE, dateTime);
        if (mState == STATE_INSERT) {
            if (title == null) {
                int length = text.length();
                title = text.substring(0, Math.min(30, length));
                if (length > 30) {
                    int lastSpace = title.lastIndexOf(' ');
                    if (lastSpace > 0) {
                        title = title.substring(0, lastSpace);
                    }
                }
            }
            values.put(NotePad.Notes.COLUMN_NAME_TITLE, title);
        } else if (title != null) {
            // In the values map, sets the value of the title
            values.put(NotePad.Notes.COLUMN_NAME_TITLE, title);
        }
        values.put(NotePad.Notes.COLUMN_NAME_NOTE, text);
        getContentResolver().update(
                mUri,    // The URI for the record to update.
                values,  // The map of column names and new values to apply to them.
                null,    // No selection criteria are used, so no where columns are necessary.
                null     // No where columns are used, so no where arguments are necessary.
            );
    }  
```  
运行效果截图：  




