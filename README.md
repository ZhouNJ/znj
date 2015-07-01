# znj
package {
	import flash.display.*;
	import flash.events.*;
	import flash.text.*;
	import flash.utils.Timer;
	import flash.utils.getDefinitionByName;

	public class CatchingGame extends MovieClip {
		var catcher:Catcher;
		var nextObject:Timer;
		var objects:Array = new Array();
		var score:int = 0;
		const speed:Number = 7.0;
		
		public function CatchingGame() {
			catcher = new Catcher();
			catcher.y = 350;
			addChild(catcher);
			setNextObject();
			addEventListener(Event.ENTER_FRAME, moveObjects);
		}
		
		public function setNextObject() {
			nextObject = new Timer(1000+Math.random()*1000,1);
			nextObject.addEventListener(TimerEvent.TIMER_COMPLETE,newObject);
			nextObject.start();
		}

		public function newObject(e:Event) {
			var goodObjects:Array = ["Circle1","Circle2"];
			var badObjects:Array = ["Square1","Square2"];
			if (Math.random() < .5) {
				var r:int = Math.floor(Math.random()*goodObjects.length);
				var classRef:Class = getDefinitionByName(goodObjects[r]) as Class;
				var newObject:MovieClip = new classRef(); 
				newObject.typestr = "good";
			} else {
				r = Math.floor(Math.random()*badObjects.length);
				classRef = getDefinitionByName(badObjects[r]) as Class;
				newObject = new classRef(); 
				newObject.typestr = "bad";
			}
			newObject.x = Math.random()*500;
			addChild(newObject);
			objects.push(newObject);
			setNextObject();
		}
		
		public function moveObjects(e:Event) {
			for(var i:int=objects.length-1;i>=0;i--) {
				objects[i].y += speed;
				if (objects[i].y > 400) {
					removeChild(objects[i]);
					objects.splice(i,1);
				}
				if (objects[i].hitTestObject(catcher)) {
					if (objects[i].typestr == "good") {
						score += 5;
					} else {
						score -= 1;
					}
					if (score < 0) score = 0;
					scoreDisplay.text = "Score: "+score;
					removeChild(objects[i]);
					objects.splice(i,1);
				}
			}
			
			catcher.x = mouseX;

		}
	}
}
