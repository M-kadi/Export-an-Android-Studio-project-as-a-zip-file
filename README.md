# Export an Android Studio project as a zip file
## How to export an Android Studio project as a zip file?

You can Export Project as a zip file

you can do it : "build.gradle" in root Project


```
task myZipApp(type: Zip) {
    from projectDir
    exclude 'build'
    exclude '.gradle'
    exclude '.idea'
    // you can comment .git if you use git
    exclude '.git'


    subprojects.findAll{
        exclude it.name + '/build'
        exclude it.name + '/.cxx'
    }


    Date date = new Date()
    String datePart = date.format("yyyy-MM-dd")
    String timePart = date.format("HH-mm-ss")

    archiveName  'my_app ' + datePart + '---' + timePart + '.zip' 

    destinationDir(file('E:/versions'))
}
```


if your project use a shared library

```
task myZipApp(type: Zip) {

    Date date = new Date()
    String datePart = date.format("yyyy-MM-dd")
    String timePart = date.format("HH-mm-ss")

    def fileName = 'my_app ' + datePart + '---' + timePart + '.zip'


    from(projectDir) {
        into("my_app")
        exclude 'build'
        exclude '.gradle'
        exclude '.idea'
        // you can comment .git if you use git
        exclude '.git'


        subprojects.findAll {
            exclude it.name + '/build'
            exclude it.name + '/.cxx'
        }

    }

    //-----------
    from('../MyLib') {
        into("MyLib")
        exclude 'build'
        exclude '.gradle'
        exclude '.idea'
        // you can comment .git if you use git
        exclude '.git'

        exclude 'mylib/build'
        exclude 'mylib/.cxx'
    }


    archiveName fileName
    destinationDir(file('E:/versions'))


    doLast {
        println("zip file ==> " + fileName)
    }
}
```

If you want to add “.git” folder to zip file, you must change the “default exclude”

```
import org.apache.tools.ant.taskdefs.condition.Os
import org.apache.tools.ant.DirectoryScanner
task myZipApp(type: Zip) {
    DirectoryScanner.defaultExcludes.each {       
      DirectoryScanner.removeDefaultExclude it 
    }
    DirectoryScanner.addDefaultExclude 'build'
    DirectoryScanner.addDefaultExclude '.gradle'
    DirectoryScanner.addDefaultExclude '.idea'

    doLast {
        DirectoryScanner.resetDefaultExcludes()
    }

    from projectDir


    subprojects.findAll{
        exclude it.name + '/build'
        exclude it.name + '/.cxx'
    }

    Date date = new Date()
    String datePart = date.format("yyyy-MM-dd")
    String timePart = date.format("HH-mm-ss")



    archiveName  'my_app' + datePart + '---' + timePart + '.zip' 
    
    if (Os.isFamily(Os.FAMILY_WINDOWS)) {
        destinationDir(file('E:/versions'))
    }
    else if (Os.isFamily(Os.FAMILY_MAC)) {
        destinationDir(file('/Users/Appel/AndroidStudioProjects/prayer_time_versions'))
    }
    
}
```

** My Test : myapp + mylib (Using Git) **

```
import org.apache.tools.ant.DirectoryScanner
import org.apache.tools.ant.taskdefs.condition.Os

task myZipApp(type: Zip) {


    Date date = new Date()
    String datePart = date.format("yyyy-MM-dd")
    String timePart = date.format("HH-mm-ss")

    def fileName = 'myapp ' + datePart + '---' + timePart + '.zip'

    DirectoryScanner.defaultExcludes.each { DirectoryScanner.removeDefaultExclude it }
    DirectoryScanner.addDefaultExclude 'build'
    DirectoryScanner.addDefaultExclude '.gradle'
    DirectoryScanner.addDefaultExclude '.idea'
    DirectoryScanner.addDefaultExclude '.cxx'


    doLast {
        DirectoryScanner.resetDefaultExcludes()
        println("zip file ==> " + fileName)
    }


    from(projectDir) {
        into("myapp")

        subprojects.findAll {
            exclude it.name + '/build'
            exclude it.name + '/.cxx'

        }

    }

    //-----------
    from('../MyLib') {
        into("MyLib")
        exclude 'build'
        exclude '.gradle'
        exclude '.idea'
        exclude '.git'

        exclude 'mylib/build'
        exclude 'mylib/.cxx'
    }


    // task myZipApp : check os family windows
    if (Os.isFamily(Os.FAMILY_WINDOWS)) {
        destinationDir(file('E:/versions'))
    }
    else if (Os.isFamily(Os.FAMILY_MAC)) {
        destinationDir(file('/Users/Appel/AndroidStudioProjects/prayer_time_versions'))
    }


}

```
