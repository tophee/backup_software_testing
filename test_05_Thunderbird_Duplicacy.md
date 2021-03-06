## Test #5

## Objective

The purpose of this test is to evaluate if the setup with 1 MB variable chunks applies well to backup a repository with a Thunderbird profile. Test 04 showed that 1Mb fixed chunks works well for databases (and there are some SQLite files in a TB profile), but most of the repository consists of Mbox files, which are text files with "stacked" messages, .

## Test parameters

* Repository: A Thunderbird profile folder, with ~23 Gb, of which the Mbox files represents 19 Gb (82%), and the largest SQLite file has approximately 450 Mb.
* Storage: One local folder.
* Chunks: 1 Mb variable.

## Method

* Perform the full initial backup of the entire folder.
* Proceed with normal daily use for a few days, that is, sending and receiving emails from multiple profile accounts, and run a daily incremental backup.
* On the 3rd day, delete a large number of (obsolete) messages and evaluate the impact.
* On the 4th day will be removed one of the largest accounts.
* Improve the inclusion / exclusion patterns during the test to evaluate the impact.
* The repository and storage sizes were measured with Rclone [(www.rclone.org)](http://www.rclone.org).
* No ```prune``` command will be executed.

## Results

In the first days, performance was as expected. 

On the 3rd day, a lot of messages were deleted, and the backup grew almost 2Gb, that is, a lot of *new* chunks had to be uploaded.
```All chunks: 19620 total, 1404 new```

Then, on the 4th day, the account was removed, and the repository (obviously) dropped about 8Gb (approximately the size of the account), but there were about 140 Mb of upload.

It's worth noting that deleting the messages and deleting the account are not equivalent operations. When the messages are deleted, they are deleted "inside" the mbox files, but they remain there, only the index changes. When the account is deleted, all related mbox files are effectively deleted.

In the following days the include / exclude patterns have been improved, and the graph shows the backup reduction to about 9 Gb. Basically, redundant files were no longer processed. Ex: "starred" sets, which have a copy of the original messages marked with a "star" (see Gmail).

The first graph shows that even with decreasing repository size (with the removal of files on the third day), the storage space does not immediately reduce without a ```prune```, which is obvious at first, but not all people realize this.

![Graph01][1]

This second chart shows that the actual size (reported by Rclone) is not the same as that reported by the Duplicacy log, which is associated with chunks.

![Graph02][2]

![Graph03][3]

The same chart above, but without the big negative bar on day 4, just to improve the visualization.

![Graph04][4]

![Graph05][5]


## Conclusions

* The orange rectangle in Figure 4 shows a recurrent backup behavior on "normal use" days, that is, **for each increase in the repository, storage increases (on average) 14 times the increment of the repository**.

* Deleting messages also represent an increase in storage (day 2: ```22.374.000```  =>  day 3: ```24.324.000```), since a lot of new chunks seems do be generated (```All chunks: 19620 total, 1404 new```).

## A plus

At the end of the test, I made a restore to visually compare the impact of the include and exclude patterns.

The blue blocks are the Mbox files, and the big green block below represents the SQLite file. Notice the size differences indicated by the arrows

**REPOSITORY:**

![Repository][6]

**RESTORED:**

![Restored][7]

## 

  [1]: https://raw.githubusercontent.com/TowerBR/backup_software_testing/master/images/test05/tb1.png
  [2]: https://raw.githubusercontent.com/TowerBR/backup_software_testing/master/images/test05/tb2.png
  [3]: https://raw.githubusercontent.com/TowerBR/backup_software_testing/master/images/test05/tb3.png 
  [4]: https://raw.githubusercontent.com/TowerBR/backup_software_testing/master/images/test05/tb4.png 
  [5]: https://raw.githubusercontent.com/TowerBR/backup_software_testing/master/images/test05/tb5.png 
  [6]: https://raw.githubusercontent.com/TowerBR/backup_software_testing/master/images/test05/tb_source_editado.png 
  [7]: https://raw.githubusercontent.com/TowerBR/backup_software_testing/master/images/test05/tb_restore_editado.png 

## Full data

| Day | REPOSITORY size     by Rclone     (kb) | REPOSITORY increase     (kb) | STORAGE size by Duplicacy log     (kb) | STORAGE size by Rclone     (kb) | STORAGE increase     (kb)  | Version | uploaded     (All chunks      log line) (kb) | Backup time |
|-----|----------------------------------------|------------------------------|----------------------------------------|---------------------------------|----------------------------|---------|----------------------------------------------|-------------|
| 01  | 23.059.648                             |                              | 21.994.000                             | 14.636.361                      |                            | 1       | 13.017.000                                   | 00:29:11    |
| 02  | 23.416.832                             | 357.184                      | 22.374.000                             | 15.379.002                      | 742.641                    | 3       | 725.234                                      | 00:08:16    |
| 03  | 25.424.742                             | 2.007.910                    | 24.324.000                             | 16.894.134                      | 1.515.132                  | 4       | 1.444.000                                    | 00:07:23    |
| 04  | 17.532.510                             | -7.892.232                   | 16.797.000                             | 16.945.890                      | 51.756                     | 5       | 144.797                                      | 00:03:19    |
| 05  | 17.472.599                             | -59.911                      | 16.751.000                             | 17.265.174                      | 319.284                    | 7       | 164.742                                      | 00:01:47    |
| 06  | 17.478.911                             | 6.312                        | 16.786.000                             | 17.371.370                      | 106.196                    | 8       | 103.706                                      | 00:01:49    |
| 07  | 17.487.807                             | 8.896                        | 9.703.000                              | 17.475.382                      | 104.012                    | 9       | 101.573                                      | 00:00:54    |
| 08  | 17.502.890                             | 15.083                       | 9.594.000                              | 17.593.547                      | 118.165                    | 10      | 115.394                                      | 00:01:00    |
| 09  | 17.505.851                             | 2.961                        | 9.186.000                              | 17.658.083                      | 64.536                     | 11      | 63.022                                       | 00:00:29    |


*Note: Some revisions are not present because they were manual backup runs between scheduled hours, with no changes in the repository.*



[![Analytics](https://ga-beacon.appspot.com/UA-113708097-1/test_05?pixel)](https://github.com/igrigorik/ga-beacon)
