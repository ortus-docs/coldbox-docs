# Usage

Once your custom interception or event points are registered and CFC are registered then you can write the methods for listening to those events:

```js
component{

	function onLog(event,interceptData,buffer){
		// your code here
	}

	function onRecordInserted(event,interceptData,buffer){
		// your code here
	}

}
```

