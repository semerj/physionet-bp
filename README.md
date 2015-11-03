# Continuous Blood Pressure Prediction

**Major datasets:**

* [MIMIC II Clinical database](https://physionet.org/works/MIMICIIClinicalDatabase/files/) (login required)
* [MIMIC II Waveform Matched Subset database](https://www.physionet.org/physiobank/database/mimic2wdb/matched/) (550 GB)

For each of the 3000+ patients, there are multiple text files. 

**Files of interest:**

* `MEDEVENTS`
* `ADDITIVES`
* `PEO_MED`
* `POE_ORDER`
* `DEMOGRAPHIC_DETAIL`

**Medications of interest:**

1. vasodilators:
    * nitroglycerine-k (121)
    * nitroglycerine (49)
    * nitroprussid (50)
2. vasoconstrictors: 
    * neosynephrine-k (128)
    * epinephrine-k (119)
    * neosyneprine (127)
    * epinephrine drip (309)
    * dobutamine (42)
    * epinephrine (44)

### Select Patients Treated with vasodilators & vasoconstrictors from MIMIC II Clinical Database

It's better to search the name of all the interested drugs in the file `POE_MED` or `POE_ORDER`. In the `MEDEVENTS` file, the drugs are represented by the codes in the parenthesis.

We can merge `POE_MED` with `POE_ORDER` to get the duration and dose of nitroglycerine in one file, after we decide to pick the record of specific patient.

### Merge clinical data with MIMIC II Waveform Matched Subset

The next task is to merge the selected clinical data with the MIMIC II Waveform Matched Subset. This is free access, but very large if we wish to download all. The description page has some codes for downloading. 

To visualize the waveform data, you can go to [PhysioNet ATM](https://www.physionet.org/cgi-bin/atm/ATM) and go to the Input database drop down menu and click MIMIC II Waveform Matched Subset (`mimic2wdb/matched`) and choose the samples in the below drop down menu. To merge the subset with the clinical data, we need to determine the subject_id that is shared by both. We also need to align the time of different data files.

## Converting Waveform Data

After identifying a patient that received a vasoconstrictor/vasodilator, download their [signal data](https://www.physionet.org/physiobank/database/mimic2wdb/matched/s00177/3555290_0001.dat) and its [header](https://www.physionet.org/physiobank/database/mimic2wdb/matched/s00177/3555290_0001.hea), e.g. record 00177 ([full data](https://www.physionet.org/physiobank/database/mimic2wdb/matched/s00177/)).

We'll also need to install the [WFDB toolkit](https://physionet.org/physiotools/wfdb-linux-quick-start.shtml) to able to convert the signal data into other formats (additional Physionet software [here](https://physionet.org/physiotools/software-index.shtml)). The Github repo should contain a Vagrantfile that will create an Ubuntu image and automatically install the requirements for the WFDB toolkit. Start vagrant:

```sh
$ vagrant up
$ vagrant ssh
```

#### 1. Matlab (`.mat`)

Use `wfdb2mat` to convert into Matlab's native format. You can then import the `.mat` file into Python using `scipy.io.loadmat`.

```sh
# need 3555290_0001.dat and 3555290_0001.hea in same directory
$ wfdb2mat -r 3555290_0001
# this outputs 3555290_0001m.hea and 3555290_0001m.mat, which can be read into Python
```

#### 2. CSV/Text

Use `rdsamp` with the `-c` flag to convert the data into CSV format ([rdsamp manual](https://physionet.org/physiotools/wag/rdsamp-1.htm)). This create a very large CSV file compared to the original/Matlab data formats.

```sh
$ rdsamp -r 3555290_0001 -pd -c > s00177.csv
```
