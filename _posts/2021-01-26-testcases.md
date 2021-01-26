---
layout: post
title: "Testing existing codebase"
---

Hey Everyone, in the last two weeks I have been testing Elijah's code on various test cases. (Elijah Newren is working on merge-ort and his work also includes optimization in rename detection). I have already tested Elijah's code on directory rename, file basenames rename and file modifications and renames using hyperfine[1] as the tool to measure time.

To run the test case, first of all we need to set up the linux kernel repo:

`git clone git://git.kernel.org/pub/scm/linux/kernel/git/stable/linux-stable.git`

Steps to run the testcases in linux-kernel repo:

**Directory Renames**

=====Basic Setup=====

```
git switch -c 5.4-renames v5.4
git mv drivers pilots  
git commit -m "Rename drivers/ to pilots/"
git config merge.renameLimit 30000
git config merge.directoryRenames true
```

=====Tests=====

For Git v2.30:

`hyperfine --warmup 1 -p "git checkout -q 5.4-renames; git branch -f hwmon-updates fd8bdb23b91876ac1e624337bb88dc1dcc21d67e; git branch -f hwmon-just-one fd8bdb23b91876ac1e624337bb88dc1dcc21d67e~34" "git rebase --onto HEAD 4703d9119972bf586d2cca76ec6438f819ffa30e hwmon-updates " --show-outputs` 

For remerge-diff[2] branch:

`hyperfine --warmup 1 -p "git checkout -q 5.4-renames; git branch -f hwmon-updates fd8bdb23b91876ac1e624337bb88dc1dcc21d67e; git branch -f hwmon-just-one fd8bdb23b91876ac1e624337bb88dc1dcc21d67e~34" "~/projects/git/bin-wrappers/test-tool fast-rebase --onto HEAD 4703d9119972bf586d2cca76ec6438f819ffa30e hwmon-updates " --show-output` 


=====Output=====

*For Git v2.30:*

Time (mean ± σ): 13049.287 s ± 424.787 s [User: 12119.514 s, System: 57.612 s]
Range (min … max): 12560.397 s … 13328.138 s 3 runs

*For Remerge diff:*

Time (mean ± σ): 1.046 s ± 0.067 s [User: 849.7 ms, System: 95.5 ms]
Range (min … max): 0.888 s … 1.125 s 10 runs

**File basename renames**

=====Basic Setup=====

```
git switch -c test_file_rename v5.4
cd drivers
a=1

for d in accessibility/ crypto/ memory/ slimbus/ pinctrl/ vlynq/ sbus/
virt/ zorro/
do
    (
        cd "$d"
        for i in $(ls *.c | sort)
        do
            new=$(printf "%08d.c" "$a")
            mv -i -- "$i" "$new"
            a=$(expr $a + 1)
        done
    )
done

git add .
git commit -m "Rename some file basenames"
git config merge.renameLimit 30000
git config merge.directoryRenames true
```

=====Tests=====

For Git v2.30:

`hyperfine --warmup 1 -p "git checkout -q test_file_rename; git branch -f hwmon-updates fd8bdb23b91876ac1e624337bb88dc1dcc21d67e; git branch -f hwmon-just-one fd8bdb23b91876ac1e624337bb88dc1dcc21d67e~34" "git rebase --onto HEAD 4703d9119972bf586d2cca76ec6438f819ffa30e hwmon-updates " --show-outputs` 

For remerge-diff branch:

`hyperfine --warmup 1 -p "git checkout -q test_file_rename; git branch -f hwmon-updates fd8bdb23b91876ac1e624337bb88dc1dcc21d67e; git branch -f hwmon-just-one fd8bdb23b91876ac1e624337bb88dc1dcc21d67e~34" "~/projects/git/bin-wrappers/test-tool fast-rebase --onto HEAD 4703d9119972bf586d2cca76ec6438f819ffa30e hwmon-updates " --show-output` 


=====Output=====

*For Git v2.30:*

Time (mean ± σ): 20.679 s ± 1.756 s [User: 18.687 s, System: 1.447 s]
Range (min … max): 19.350 s … 24.223 s 10 runs

*For Remerge diff:*

Time (mean ± σ): 235.3 ms ± 7.7 ms [User: 147.3 ms, System: 69.1 ms]
Range (min … max): 226.6 ms … 246.9 ms 10 runs

**File modifications and renames**

=====Basic Setup=====

```
git switch -c test_file_modify v5.4
cd drivers
a=1

for d in $(find . -maxdepth 3 -type d)
do
    (
        cd $d
        for i in $(ls *.c | sort)
            do
                echo "text" >> $i
                new=$(printf "%08d.c" "$a")
                mv -i -- "$i" "$new"
                let a=a+1
            done
    )
done

git add .
git commit -m"Add some renames with modifications."
git config merge.renameLimit 30000
git config merge.directoryRenames true
```

=====Tests=====

For Git v2.30:

`hyperfine --warmup 1 -p "git checkout -q test_file_modify; git branch -f hwmon-updates fd8bdb23b91876ac1e624337bb88dc1dcc21d67e; git branch -f hwmon-just-one fd8bdb23b91876ac1e624337bb88dc1dcc21d67e~34" "git rebase --onto HEAD 4703d9119972bf586d2cca76ec6438f819ffa30e hwmon-updates " --show-outputs` 

For remerge-diff branch:

`hyperfine --warmup 1 -p "git checkout -q test_file_modify; git branch -f hwmon-updates fd8bdb23b91876ac1e624337bb88dc1dcc21d67e; git branch -f hwmon-just-one fd8bdb23b91876ac1e624337bb88dc1dcc21d67e~34" "~/projects/git/bin-wrappers/test-tool fast-rebase --onto HEAD 4703d9119972bf586d2cca76ec6438f819ffa30e hwmon-updates " --show-output` 


=====Output=====

*For Git v2.30:*

Time (mean ± σ): 14913.690 s ± 375.941 s [User: 14512.504 s, System: 54.079 s]
Range (min … max): 14647.859 s … 15179.520 s 

*For Remerge diff:*

Time (mean ± σ): 5.300 s ± 0.002 s [User: 4.523 s, System: 0.631 s]
Range (min … max): 5.298 s … 5.301 s 

## Next?

After running these testcases I am trying to extrapolate them on a smaller repository like Git and to do that I am looking for test case that can show performance improvement(Or maybe deterioration) so that I can add that performance test in the already existing perf tests in Git repo. For that I have to look for a range of commits that can cleanly rebase onto an older base and am currently looking into it.

I would like to thank Elijah for constantly helping me and giving me all the explanations required on his work. I would also like to thank my mentors, Christian Couder and Kaartic Sivaraam for supporting me.


References:

[1] Hyperfine: [sharkdp/hyperfine](https://github.com/sharkdp/hyperfine)

[2] There are two brances, 'ort' and 'remerge-diff' in Elijah's clone for Git (https://github.com/newren/git). We can use any of the branches. The former only makes the 'ort' strategy available as an alternative but does not make it the default.  The latter makes it the default, and has a --remerge-diff option to `git log` and also makes `git log -p` imply `git log -p --remerge-diff`. (As said by Elijah)
