# Flex Integration

This is the last step of the guide, so let's review for a moment.

* We have configured the application to return data/objects from the event handlers
* We have customized our proxy and made sure it inherits from the base proxy.
* We have created some event handler methods
* We learned how to determine when a remote call was made via the event object

Now our last step is to actually show some remote calls. For this example, I will be showing some Flex code. I am no big Flex expert, but below are some of the flex sample codes on how to call the ColdBox proxy. I recommend encapsulating the calls to the ColdBox proxy into a class of its own as a delegate class or however you see it fit in your Flex application architecture. The sample below is very flat.

```js
<?xml version="1.0" encoding="utf-8"?>
<mx:Application xmlns:mx="http://www.adobe.com/2006/mxml" layout="absolute">
	
<mx:Script>
<![CDATA[
	import mx.rpc.events.FaultEvent;
	import mx.events.ItemClickEvent;
	import mx.rpc.events.ResultEvent;
	import mx.controls.Alert;
	import mx.collections.ArrayCollection;
	
	//My Proxy Path
	public var cbProxyPath:String = "coldbox.samples.applications.ColdboxFlexTester.webroot.coldboxproxy";
	
	/* PUBLIC FAULT Handler*/
	public function faultHandler(event:FaultEvent):void{
		Alert.show(event.fault.toString());
	}
	/* UTILITY METHOD TO GET A CBPROXY OBJECT, You can separate all this to a delegate class*/
	public function getColdBoxProxy():RemoteObject{
		var cProxy:RemoteObject = new RemoteObject( "ColdFusion" );
		cProxy.source= cbProxyPath;
		cProxy.showBusyCursor = true;
		cProxy.addEventListener( FaultEvent.FAULT, faultHandler );
		return cProxy;
	}
	/* Read the Cache Objects*/
	public function handleCacheResults(event:ResultEvent):void{
		var cacheItems:Object = new Object();
		var key:String;
		var array:Array = new Array();
		
		cacheItems = event.result;
		for( key in cacheItems ){
			array.push( { item:key, total: cacheItems[key] });
		}
		var collection:ArrayCollection = new ArrayCollection(array);
		cachechart.dataProvider = collection;
	}
	/* Call the proxy for cache objects*/
	public function readCache():void{
		var cProxy:RemoteObject = getColdBoxProxy();
		cProxy.process.addEventListener("result",handleCacheResults );
		cProxy.process({event:"ehFlex.getCacheItemTypes"});
	}
	]]>
</mx:Script>

	<mx:PieChart id="cachechart"
            height="190"
            width="205"
            showDataTips="true"  x="10" y="450">
        <mx:series>
            <mx:PieSeries field="total" nameField="item"
                labelPosition="callout" />
        </mx:series>
    </mx:PieChart>
    <mx:Button x="45" y="420" label="Get Cache Chart" click="readCache()"/>

</mx:Application>
```

So what does this application do. Well let's start from the top. The first part of the application just imports some classes for us to use. Then we declare the path to our coldbox proxy:

```js
//My Proxy Path
public var cbProxyPath:String = "coldbox.samples.applications.ColdboxFlexTester.webroot.coldboxproxy";
```

We then setup a public default error handler:

```js
/* PUBLIC FAULT Handler*/
public function faultHandler(event:FaultEvent):void{
	Alert.show(event.fault.toString());
}
```
We then declare our mini proxy delegate. Again, this is for sample purposes, you must encapsulate this into a class of its own and even expand on it to make it a true delegate.

```js
/* UTILITY METHOD TO GET A CBPROXY OBJECT, You can separate all this to a delegate class*/
public function getColdBoxProxy():RemoteObject{
	var cProxy:RemoteObject = new RemoteObject( "ColdFusion" );
	cProxy.source= cbProxyPath;
	cProxy.showBusyCursor = true;
	cProxy.addEventListener( FaultEvent.FAULT, faultHandler );
	return cProxy;
}
```
