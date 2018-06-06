When using Choregraphe to create your Pepper apps, you can put Python code in boxes.

If the code gets longer, boxes become hard to edit. They are also hard to version.

Also, if you find a library online you can use in your app, it's easier to just download the library folder/files than copying them in boxes.

So how do you import Python files in your Pepper apps using Choregraphe?

1- When you play or install an application on your robot, the folder shown in project content is uploaded on the robot. So that's where your Python file will be.

2- When your behavior/activity is playing, you can get the behavior's directory by using ALFrameManager.getBehaviorPath.

3- now you should find the relative path between the Behavior path and your python file. In the video example, it was simply '..'.

4- we add that folder to the Python path when we start, and erase it when we quit.

The code for adding and removing the folder to your Python path is as below. The "File Name" refers to the parameter of the box, which in the video is '/..'.

class MyClass(GeneratedClass):
    def __init__(self):
        GeneratedClass.__init__(self, False)

    def onLoad(self):
        self.framemanager = ALProxy("ALFrameManager")
        self.folderName = None


    def onInput_onStart(self):
        self.folderName = self.framemanager.getBehaviorPath(
                                           self.behaviorId)
                              + self.getParameter("File name")
        import sys
        if self.folderName not in sys.path:
            sys.path.insert(0, self.folderName)
        self.onStopped()

    def onUnload(self):
        import sys
        if self.folderName and self.folderName in sys.path:
            sys.path.remove(self.folderName)



As long as this box is started and not unloaded, you can import your module from anywhere.

A few things to be careful about:

Don't ever import stuff outside of the class definition. In Naoqi 2.X There is only 1 Python session shared by all the apps on the robot. When an app is started, it is loaded into that 1 Python session. So whatever you import there is imported for everyone, everywhere.

When developing and editing your Python library, don't forget to use reload(myModule), otherwise you might just reuse an older version of your module (same as 1: the module was already imported by previous time you started the app).

If you create your app entirely in a Python file, don't forget to create a link to exit the app... Simple but otherwise your app will run forever.

Great reference video that explains: https://youtu.be/orDWxHQxw5s
