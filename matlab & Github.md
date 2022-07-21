# matlab & github

- 安装Git
- 在matlab中将当前工作路径指向某个空文件夹*toGithub*
- 右击空白处，source control -> Manage Files
- Repository path: 某个**public** repository的https路径
- 点击Retrieve后，即自动clone

## push
- create a new file in matlab, if name as ***new.m***, it's statu of the Git is a `empty circle ⚪` when the file created.
- select ***new.m*** then right click and select **source control** then **Add to Git**, the statu change to the `plus ➕` from ⚪ after finished.
- secondary, **View and Commit Changes** from **source control**, enter the comment then commit, then the statu was change to `the circle fill with green 🟢`, but notice, we no yet upload the **new.m** to Github so far.
- finally, **Push** from the **source control**, then request for username and password, fill it and then finish the push.


在REC因为Git路径无法添加到环境变量，因此无法完成Push。

Push之前要先Pull，以免冲突


代码操作：
```
!git status
!git add .
!git commit -m "up"
!git pull
!git push
```


## 删除本地文件

- 在matlab中选中该文件，source control -> Delete from Git and disk
- 在matlab中选中该文件，source control -> View and commit changes
- push
- finish
