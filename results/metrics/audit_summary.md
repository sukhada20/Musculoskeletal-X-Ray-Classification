# Dataset audit summary

Images scanned: 41399; unreadable: 63

## Class counts
  dataset         split label_name     n  pct_of_split
fracatlas external_test   abnormal   719         17.87
fracatlas external_test     normal  3305         82.13
     mura          test   abnormal  1364         49.10
     mura          test     normal  1414         50.90
     mura         train   abnormal 12758         40.99
     mura         train     normal 18364         59.01
     mura           val   abnormal  1401         41.06
     mura           val     normal  2011         58.94

## Issues
                              check severity     n                                                                   detail
                     count_mismatch  WARNING -2020                           ('mura', 'train'): found 31126, reported 33146
                     count_mismatch  WARNING  -250                               ('mura', 'val'): found 3412, reported 3662
                     count_mismatch  WARNING  -419                              ('mura', 'test'): found 2778, reported 3197
                  unreadable_images  WARNING    63          excluded in memory only (files untouched); see audit_images.csv
exact_duplicates_across_mura_splits BLOCKING     1                       same bytes appear in more than one split (leakage)
 near_duplicates_across_mura_splits  WARNING   232 identical dHash across splits (inspect manually; may be false positives)
                duplicate_filenames     INFO 37301        same basename in different folders (paths are used as unique ids)
            manifest_label_mismatch BLOCKING 15523                                   mura/label disagrees with folder label
          patient_overlap_train_val BLOCKING     6                   same patient in train and val -> optimistic validation
     patient_key_overlap_train_test  WARNING     6      keys include the original split, so overlap here means a real clash