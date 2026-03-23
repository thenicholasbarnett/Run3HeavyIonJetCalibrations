# Heavy Ions Jet Calibrations

This repository contains everything needed to apply jet energy corrections to jets for the Run 3 PbPb collisions, as well as the Run 3 pp reference run performed shortly before the PbPb collisions, for the heavy ion physics analysis group of CMS.
Inside this repository are text files containing the various jet calibrations for various clustering algorithms with both pp and PbPb reconstructions.
There is also a header file in this repository that can be used to apply the various corrections, when they're formatted like the text files here.
These jet energy corrections ultimately change the transverse momentum of each jet the correction(s) are applied.

The correction text files in this repository are currently just L2Relative or MC truth corrections as well as dijet residuals or L2Residual corrections.
MC truth corrections are used on jets clustered from both MC and DATA samples, and the dijet residuals are only applied to jets clustered from DATA samples.
The corrections with PbPb reconstruction here are applied to jets in datasets for PbPb collisions.
Similarly, the corrections here with pp reconstruction are used on jets in the pp reference datasets.

## Applying Jet Energy Corrections

To apply these corrections to a jet from a forest follow the steps below.

1. Include the header file used to apply the corrections.
```
#include "JetCorrector.h"
```
2. Input the correction text files that are going to be applied to the jets in the forests being analyzed.
The example below is for AK4PF jets in 2023 PbPb data.
```
vector<string> Files;
Files.push_back("correction_text_files/L2Relative_AK4PF_PbPbReco_v0_2_13_2024.txt");
Files.push_back("correction_text_files/L2Residuals_AK4PF_2023ppRef_PbPbReco_v0_10_8_2025.txt");
JetCorrector JEC(Files);
```
3. Perform the correction on the transverse momentum of a single jet.
In the example below rawpt[j], jtphi[j], and jteta[j] are the ttree specific variables for a single jet in a single event. The jet_pt_corr is the corrected transverse momentum of the same jet the input variables are from.
```
JEC.SetJetPT(rawpt[j]);
JEC.SetJetEta(jteta[j]);
JEC.SetJetPhi(jtphi[j]);  
Float_t jet_pt_corr = JEC.GetCorrectedPT();
```

## Getting Jet Uncertainties

In order to get the up and down variations for jet measurements use the following method.

1. Include the JetUncertainty header file.
```
#include "JetUncertainty.h"
```
3. Input the uncertainty text file associated to the dataset being analyzed. 
The example below is for jets in 2023 PbPb datasets.
```
JetUncertainty JEU("Spring23PbPb_TotalUncertainties.txt");
```
5. Set the momentum of the JetUncertainty header object (JEU) to be the corrected momentum of the jet. Then get the up and down variations out of the JEU.
```
JEU.SetJetPT(jet_pt_corr);
JEU.SetJetEta(jteta[j]);
JEU.SetJetPhi(jtphi[j]);

double CorrectedPT_Down = jet_pt_corr * (1 - JEU.GetUncertainty().first);
double CorrectedPT_Up = jet_pt_corr * (1 + JEU.GetUncertainty().second);
```
