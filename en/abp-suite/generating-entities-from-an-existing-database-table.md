# Generating Entities from an existing Database Table to Create a CRUD Page

If you have an existing database table, you can generate the entities using ABP Suite and create a CRUD page based on those entities, let's get started.

# Launch ABP Suite

To start ABP Suite, type the following command in the command line:

```
abp suite
```

This will launch ABP Suite onto your browser.

# Create/open your project

![image-20220228120525546](C:\Users\BetterCallHamza\AppData\Roaming\Typora\typora-user-images\image-20220228120525546.png)

Either create a new project or open an existing project that's based on an app or module template.

# Generate the Entities

After opening the project in ABP Suite, scroll down to the bottom and click the **Load Entity From Database** button:![Screenshot 2022-02-28 122404](D:\new pc\image dump\Screenshot 2022-02-28 122404.png)



This will open the window seen below, choose the data source of your database and add the connection string of your project:![image-20220228123643593](C:\Users\BetterCallHamza\AppData\Roaming\Typora\typora-user-images\image-20220228123643593.png)



Click the lightning icon to test the connection, and then connect to the database by clicking **connect**, and this should extend the window as the following window:![image-20220228144258381](C:\Users\BetterCallHamza\AppData\Roaming\Typora\typora-user-images\image-20220228144258381.png)



Uncheck the Id property since it automatically gets generated, it'll cause an error if we generate it twice, then click **OK**:![Screenshot 2022-02-28 144727](D:\new pc\image dump\Screenshot 2022-02-28 144727.png)



After that, make sure the primary key type is selected, then click **Save and generate**:![image-20220228150159716](C:\Users\BetterCallHamza\AppData\Roaming\Typora\typora-user-images\image-20220228150159716.png)



The following GIF is a summary of the previous steps: ![SUTIE GIF](D:\new pc\7m.2\asdasdasd\SUTIE GIF.gif)

# Run the Project!

After that, run the project and watch the magic! An easy CRUD app using the entities from an existing database table!![gifgiffigifig](D:\new pc\7m.2\gifgiffigifig\gifgiffigifig.gif)



## What's next?

[Accessing source code of modules](source-code.md)