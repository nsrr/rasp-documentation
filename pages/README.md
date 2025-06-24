## About

The RASP project used retrospective clinical pediatric sleep recordings from five sites. As manual staging was only present in some sites, we developed a pediatric stager based on other NSRR samples (primarily [NCHSDB](https://sleepdata.org/datasets/nchsdb/)) and auto-scored all studies consistently. As the primary focus of the RASP consortium was on EEG based signals, to study sleep micro-architecture with respect to development and neurodevelopmental disorders (NDD), the harmonized dataset presented below includes only EEG, EOG and EMG signals. The original _as is_ files contain a variety of standard PSG channels, including respiratory signals. Note that the original data were received in a relatively heterogeneous and challenging format (e.g. in some cases single night recordings split over multiple, potentially overlapping single EDFs, and also including some gapped (EDF+D) files, some inherent inconsistencies in ID conventions, differences in the montages and referencing used, and a few cases of apparent channel mislabeling). We endeavored to correct and harmonize most of these issues for the central analyses: these EDFs (i.e. standardized one-per-recording, with consistent stage annotation formats in Luna `.annot` files, etc) are presented in the [`harmonized/`](:files_path:/harmonized) folder.  The original data are presented _as is_ in the [`original/`](:files_path:/original) folder. The harmonized set will be significantly easier to work with, and should be sufficient for most purposes as long as the focus of analysis is on the sleep EEG (or EOG/EMG). 

Please see this manuscript for more information on the cohort: 

 - [Gong NN, Mahat A, Ahmad S, Glaze D, Maletic-Savatic M, McGinley M, Morse AM, Rodriguez AJ, Thurm A, Redline S, Maski K, Davis P, Purcell S, Buckley A. Leveraging clinical sleep data across multiple pediatric cohorts for insights into neurodevelopment: the retrospective analysis of sleep in Pediatric (RASP) cohorts study. Sleep. 2025 Jun 9:zsaf157. doi: 10.1093/sleep/zsaf157. Epub ahead of print. PMID: 40488421.](https://pubmed.ncbi.nlm.nih.gov/40488421/)

## Data de-identification

All personally identifiable information (PII) has been removed from the data files by the NSRR team.

## Data overview

_Harmonized dataset:_

| File/Folder | Contents | 
|----|----|
| [`harmonized/`](:files_path:/harmonized) | Primary harmonized EDFs (EEG/EOG/EMG) and staging annotations (_N_ = 1,552 pairs) | 
| [`rasp-dataset-0.1.0-harm.tsv`](:files_path:/harmonized/rasp-dataset-0.1.0-harm.tsv) | Simplified clinical & covariate data (text/TSV) |

_Original ("as is") dataset:_ 

| File/Folder | Contents | 
|----|----|
| [`original/`](:files_path:/original) | Original _as is_ full PSG data for the five site-specific sub-folders (`bch/`, `gei/`, `nih/`, `nyu/` & `tch/`)  (note: multiple split/overlapping EDFs per night for GEI and TCH, and other known issues as listed below) | 
| [`rasp-dataset-0.1.0.pre.csv`](:files_path:/original/rasp-dataset-0.1.0.pre.csv) | Clinical & covariate data (text/CSV)|

### Original data

RASP data were compiled across five sites:

| Code | Site | _N_ nights (_N_ EDFs) | Manual staging? |
|-----|-----|----|---|
| `BCH` | Boston Children's Hospital | 150 (150) | Y |
| `GEI`| Geisinger | 150 (16,116) | N |
| `NIH` | NIMH intramural | 415 (415) | Y |
| `NYU` | New York University | 50 (50) | Y |
| `TCH` | Texas Children's Hospital, Baylor College of Medicine | 872(1058) |  N |

All sleep recordings were originally deposited as EDFs, but in varying formats, as described below.

#### BCH

All data were EDF+C records, including manual staging as EDF+ annotations.  EDFs contained between 51 and 105 channels.    Each individual had exactly one EDF recording.

We extracted annotations with labels:   `Sleep_stage_N1`, `Sleep_stage_N2`, 
`Sleep_stage_N3`, `Sleep_stage_R` and `Sleep_stage_W` (as well as a smaller number of `Sleep_stage_1`, `Sleep_stage_N` and `Sleep_stage_?`).

#### GEI

All records were standard EDF files; however, the N=150 individuals had recordings spread over multiple contiguous segments.  These were typically 4 minutes, but sometimes 1 hour; most were under 5 minutes. In total, there were 16,116 EDFs associated with the 150 GEI individuals.  

For example, the individual with IID (individual ID) code `GEIS0002` had 106 EDFs (`GEIS0002-000000[001].edf`, `GEIS0002-000000[002].edf`, etc up to `GEIS0002-000000[106].edf`).

|EDF|                          Clock time       |       Duration|
|---|---|---|
|`GEIS0002-000000[001].edf`  |   20.01.29 - 20.06.29  |   00:05:00  300 sec |
|`GEIS0002-000000[002].edf` |    20.06.29 - 20.11.29  |   00:05:00  300 sec |
|`GEIS0002-000000[003].edf` |   20.11.29 - 20.16.29   |  00:05:00  300 sec |
|...
|`GEIS0002-000000[106].edf` |    04.46.29 - 04.47.38  |   00:01:09  69 sec |

EDFs varied in the channel montage, with  individuals having between 29 and 43 channels.  Each individual had similar EDF headers. We use Luna's `--merge` to create a single EDF per individual:  e.g. effectively using commands in the form: 

```
luna --merge id=GEIS0002 edf=edfs/geis/GEIS0002.edf /path/to/edfs/GEIS0002-*.edf
```

#### NIH

NIH data were from two sources: 

  - N=90 studies, with the EEG using contra-lateral mastoid referencing, with manual staging available
  - N=325 studies, all but 4 with manual staging available  

Standard EDFs had between 16 and 27 channels.  The N=90 subset all had the following channels: Fp1,Fp2,F7,F8,F3,F4,T3,T4,C3,C4,T5,T6,P3,P4,O1,O2.

The other EDFs often had bipolar channels (e.g. `P3-P4`) but also largely contained contra-lateral mastoid channels for the same subsets.

#### NYU

These N=50 recordings were a mixture of EDF and EDF+C, but with uniform channel montages (51 channels).

We extracted staging when present in the EDF+C, followed by manual editing to remove occasional technician notes (e.g. 'REM' ) that overlapped with the full 30-second epoch staging also present.   We further corrected some inconsistent IDs. 

#### TCH

This was the most complex dataset, containing 872 individuals with recordings spread across 1,058 EDFs.   Furthermore:

 - some EDFs were corrupt (e.g. truncated) and so removed (e.g. `GB124` and `GB79`), dropping 10 individuals in total
 - there was a mixture of EDF+C and EDF+D (gapped) files
 - the same individual often had multiple EDFs spanning the night: sometimes, the EDFs had gaps between them, but sometimes they overlapped
 - variation in channel count, between 15 and 47 channels
 - all EDF+D files appeared to have gaps at the start

Especially given the overlap in EDFs for the same individual, we elected to pick the single longest EDF for each individual to constitute the final harmonized set.

### Harmonized data

#### EDFs

There were multiple instances across cohorts in which the ID conventions were inconsistent, and so these were fixed (e.g. dropping extraneous `-a` suffixes, or naming `patinet39` to `patient39`), so that the EDFs aligned with the demographic/clinical data and could be matched by ID. 

For each individual, we picked the longest single segment per EDF+D, and/or the longest single EDF (for individuals with multiple, possibly overlapping EDFs spanning the same night).

We saved all files as standard EDF, with an `.annot` annotation file containing manual staging (when present) only.

Several recordings were flagged (and either dropped or manually fixed) during this process.

#### EEG channels

To create a harmonized set of files, we focused on the sleep EEG channels.   Most studies had the following channels present, which were selected as the core set used in EEG-based analyses (tabulated below).  The channels `M1` and `M2` are the left and right mastoids: i.e. all data were set as contra-lateral mastoid referenced.   Most studies had `P4_M1` but not `P3_M2`, and so parietal channels were dropped from the primary harmonized analyses.  Some EDFs also contained midline electrodes, and so we also created/saved linked-mastoid referenced channels where appropriate (see below).

#### Channel mislabeling

During analysis, we observed unusual patterns of spectral power and connectivity for the TCH cohort frontal channels.   On further review, it appeared that `Fp1_M2` and `Fp2_M1` were mislabeled and should be `F3_M2` and `F4_M1` respectively.  These have been swapped in the harmonized dataset, but are _as is_ in the original datasets.


#### Automated staging (Luna/POPS)

As only three of the five cohorts had manual staging (and also, as the manual staging was performed by different raters across the different sites), to standardize analysis we automatically rescored all individuals. 

We explicitly trained a new POPS model on pediatric studies from the NSRR (called the `tri` model).  This was based on contralateral-mastoid referenced frontal, central and occipital channels (e.g. `F3`, `C3` and `O1`, as well as `F4`, `C4` and `O2`).  We resampled all channels to 128 Hz.  During training, left- and right-hemisphere channels were input separately.    During prediction, the left and right channels were paired as POPS _equivalence sets_ (i.e. predictions would be made independently from both, and the final solution would be an epoch-by-epoch blend taking the most confident prediction from each).

Some studies with fewer than five stages (e.g. with only wake/sleep scoring) were dropped from evaluations. The `tri` model will shortly be posted on the main Luna website.  See the manuscript referenced above for more details on the automated staging approach developed for RASP.

#### Final harmonized (EEG/EOG/EMG) dataset

After all exclusions, the final analytic sample comprised _N_ = 1,552 recordings (with 150, 150, 412, 50 and 790 from BCH, GEI, NIH, NYU and TCH respectively) on _N_ = 1,424 unique individuals.  These are distributed as standard EDFs (continuous, no annotation channels), with all (POPS-derived) staging annotations in the accompanying `.annot` files.

#### EDFs

In the final set, individuals had between 15 and 40 channels.

These files contain only EEG, EMG and EOG channels, as tabulated below.  EEG channels are represented as both contra-lateral mastoid referenced (e.g. `C3_M2`) and linked-mastoid referenced (e.g. `C3_LM`).   Because of how the original data were received (i.e. with pre-computed channels/referencing) not all electrodes have both referencing schemes.  In particular, midline electrodes (e.g. `Cz`) are only present in the linked-mastoid set.  

_CM-referenced channels:_

| Channel label | _N_ EDFs present |
|----|-----|
|	`Fp1_M2	`|	568	|
|	`Fp2_M1` |	568	|
| | |                 
|	`F7_M2`	|	1401	|
|	`F3_M2`	|	1551	|
|	`F4_M1`	|	1551	|
|	`F8_M1`	|	1401	|
| | |          
|	`T3_M2`	|	1399	|
|	`C3_M2`	|	1551	|
|	`C4_M1`	|	1551	|
|	`T4_M1`	|	1399	|
| | |                                
|	`T5_M2`	|	605	|
|	`P3_M2`	|	617	|
|	`P4_M1`	|	1358	|
|	`T6_M1`	|	605	|
| | |                                 
|	`O1_M2`	|	1551	|
|	`O2_M1`	|	1551	|


_LM-referenced channels:_

| Channel label | _N_ EDFs present |
|----|-----|
|	`Fp1_LM` |	939	|
|	`FPZ_LM`|	110	|
|	`Fp2_LM`	|	939	|
| | |                
|	`F7_LM`	|	988	|
|	`F3_LM`	|	971	|
|	`FZ_LM`	|	988	|
|	`F4_LM`	|	981	|
|	`F8_LM`	|	988	|
| | |                
|	`T3_LM`	|	982	|
|	`C3_LM`	|	988	|
|	`CZ_LM`	|	988	|
|	`C4_LM`	|	988	|
|	`T4_LM`	|	982	|
| | |                
|	`T5_LM`	|	192	|
|	`P3_LM`	|	200	|
|	`PZ_LM`	|	200	|
|	`P4_LM`	|	941	|
|	`T6_LM`	|	192	|
| | |                
|	`O1_LM`	|	988	|
|	`OZ_LM`	|	886	|
|	`O2_LM`	|	988	|

_EOG & EMG channels:_

| Channel label | _N_ EDFs present |
|----|-----|
|	`LOC`	|	1461	|
|	`ROC`	|	1462	|
 | | |               
|	`EMG`	|	1462	|

#### Clinical/covariate information

There two files contain data on a superset of _N_ = 1,628 RASP individuals (i.e. not all of whom are represented in the final, harmonized dataset). 

`rasp-dataset-0.1.0-harm.tsv` : _harmonized dataset of key clinical/covariate terms_

| Variable | Description |
|----|----|
| `ID` | ID that links to the selected EDF root in the harmonized file | 
| `NSRRID` | Individual ID (IID) that links to the original clinical/covariate data | 
| `RASP.SITE` | Code for which of the five RASP cohorts |
| `ENCOUNTER` | Code for which encounter (a minority of individuals have > 1 recording) |
| `AGE` | Age in years |
| `MALE` | Sex (0=F, 1=M) |
| `DIS` | Ascertained for NDD (0=N, 1=Y) |
| `ASD` | Diagnosis of ASD (0=N, 1=Y) |
| `DS` | Diagnosis of Downs (0=N, 1=Y) |


`rasp-dataset-0.1.0.pre.csv` | _original datasets of clinical/demographic covariates_

| Variable | Description |
|----|----|
|`nsrrid` |  ID |
|`encounter`   | Visit number |
|`edfid`      |  EDF file name (nb: for sites w/ multiple EDFs per individual such as TCH, this information is not correct)|
|`siteid`     | Site ID | 
|`typical`   | Flag for typical development (i.e. converse of `DIS` in the harmonized set)
|`age_years`|  Age | 
|`sex_mf` | Sex (coded `m` and `f`) |
|`diagnosis_1` | Free-form text diagnosis #1
|`diagnosis_2` | Second diagnosis, if present |
|`diagnosis_3`| Third diagnosis, if present |
| ... | | 
|`diagnosis_14`| Final diagnosis, if present |
|`dx_asd`| Has an ASD diagnosis (1=Y) |
|`dx_trisomy21`| Has a DS diagnosis (1=Y) |

Notes:

 - the harmonized covariate set has fewer individuals (the TCH individuals dropped)
 - whereas `nsrrid` in the original set aligns with `NSRRID` in the harmonized set, the EDF IDs in the original set should be ignored: i.e. they do not necessarily align with the actual EDFs the NSRR received from sites: for example, the individual with the IID `P15` has `P15.edf` listed in the original dataset, but it that does not exist: it was actually listed to `P15-2-anon.edf` (selected from `P15-1-annot.edf`,  `P15-2-annot.edf`,  `P15-3-annot.edf` and  `P15-4-annot.edf`).
 - because the original data were distributed to the NSRR at different times, some sites have varying ID schemes between the original and harmonized filesets

### Known issues

The original dataset is presented _as is_: as revealed in the harmonization process and alluded to above, there are a number of issues that must be confronted if working with the original data.   

Since originally harmonizing these RASP studies, we have introduced the Luna `EDF-MINUS` command, which can be used to streamline the creation of standard EDFs from EDF+D files.  (It will not handle the case of multiple separate _but overlapping_ files, however).  A simple strategy would be to select EDFs based on the selected harmonized for (for TCH), but retained all channels.   For GEI, it will be necessary to `--merge` the original EDFs, although this can be easily performed using Luna. 

The harmonized datasets only contain EEG, EOG and EMG signals: analyses of other (e.g. respiratory) signals will necessarily be based on these original data.

## Access and usage restrictions

The RASP dataset is available for commercial and non-commercial use. Users must have an approved NSRR data request to access RASP data.

## Citation and acknowledgement

When using this dataset, users must cite the following:

> [Zhang GQ, Cui L, Mueller R, Tao S, Kim M, Rueschman M, Mariani S, Mobley D, Redline S. The National Sleep Research Resource: towards a sleep data commons. J Am Med Inform Assoc. 2018 Oct 1;25(10):1351-1358. doi: 10.1093/jamia/ocy064. PMID: 29860441; PMCID: PMC6188513.](https://pubmed.ncbi.nlm.nih.gov/29860441/)
>
> [Gong NN, Mahat A, Ahmad S, Glaze D, Maletic-Savatic M, McGinley M, Morse AM, Rodriguez AJ, Thurm A, Redline S, Maski K, Davis P, Purcell S, Buckley A. Leveraging clinical sleep data across multiple pediatric cohorts for insights into neurodevelopment: the retrospective analysis of sleep in Pediatric (RASP) cohorts study. Sleep. 2025 Jun 9:zsaf157. doi: 10.1093/sleep/zsaf157. Epub ahead of print. PMID: 40488421.](https://pubmed.ncbi.nlm.nih.gov/40488421/)

Users must include the following text in any Acknowledgements:

> The National Sleep Research Resource was supported by the U.S. National Institutes of Health, National Heart Lung and Blood Institute (R24 HL114473, 75N92019R002). 

## Changelog

*July 2025*

- Make RASP dataset available for data requests

## References

- RASP GitHub Documentation: https://github.com/nsrr/rasp-documentation

## Questions?

Please reach out to us at support@sleepdata.org or in the [Forum](https://sleepdata.org/forum) if you have questions.
