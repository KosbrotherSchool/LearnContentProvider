在 Android 的架構裡, 每個 App 就像是獨立的個體, 彼此不互相影響,
這樣做的好處是可以避免正常的程式被不正常的程式所影響,
但程式間的資料分享就成了一個問題.
ContentProvider 就是為了解決這個 App 間資料分享的問題而生的.

ContentProvider 的功用

ContentProvider 的目的是提供一個 App 間溝通的管道, 
所以它其實有兩個層面:
1. 提供資料給其他的 App 使用
2. 取得其他 App 提供的資料

在這邊不講解如何提供資料給其他 App 使用, 有需要的話可以看看這篇.
倒是我們比較可能需要取得其他 App 提供的資料, 下邊做個整理.

Android 提供的資源

Android 本身各個App提供的資源有:
鬧鐘 (AlarmClock),
通訊錄 (Contact),
行事曆 (Calendar),
相簿 (Photo),
影音列表 (MediaStore) ...等

如此一來, 開發者便能透過 Provider 來取得手機上的資料.

ContentProvider 取資料的範例 ---  以 Contacts 為例 (原始碼連結)

一.  註冊 permission, 取得Contacts 的使用權

 <uses-permission android:name="android.permission.GET_ACCOUNTS" />  
 <uses-permission android:name="android.permission.READ_CONTACTS" />  
 <uses-permission android:name="android.permission.WRITE_CONTACTS" />  

二. 透過 Provider 取得 cursor
cursor 是Android 裡用來取得資料的物件,  取得 cursor 後就能查找我們要的任一筆資料.
這裡的 getContacts() 是範例寫的一個方法,
projection 這個 String[] 要放我們想要的欄位名稱,
selection, selectionArgs 可以給 query 資料的條件, 設 null 就表示全選.

 private Cursor getContacts()  
   {  
     Uri uri = ContactsContract.Contacts.CONTENT_URI;  
     String[] projection = new String[] {  
         ContactsContract.Contacts._ID,  
         ContactsContract.Contacts.DISPLAY_NAME  
     };  
     String selection = null;  
     String[] selectionArgs = null;  
     String sortOrder = ContactsContract.Contacts.DISPLAY_NAME + " COLLATE LOCALIZED ASC";  
     return managedQuery(uri, projection, selection, selectionArgs, sortOrder);  
   }  

三. 使用SimpleCursorAdapter 取得 Adapter

這邊的 fields 是想取得資料欄位的名稱, layout.contact_entry 則是自定義的 layout.
取得 adapter 後, 在設給 ListView 就行了.

 String[] fields = new String[] {  
      ContactsContract.Data.DISPLAY_NAME  
 };  
 SimpleCursorAdapter adapter = new SimpleCursorAdapter(this, R.layout.contact_entry, cursor,  
         fields, new int[] {R.id.contactEntryText});  
 mList.setAdapter(adapter);  
