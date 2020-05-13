JMETriggerAnalysis
==================

CMSSW Packages for JetMET Trigger Studies

### Setup

Set up CMSSW area:

 * `11_1_X`:
```shell
cmsrel CMSSW_11_1_0_pre6
cd CMSSW_11_1_0_pre6/src
cmsenv

# [HGCal] fix to PID+EnergyRegression in TICL
git cms-merge-topic cms-sw:29799

# temporary workaround for PFSimParticle::trackerSurfaceMomentum
# ref: hatakeyamak:FBaseSimEvent_ProtectAgainstMissingTrackerSurfaceMomentum
git cms-addpkg FastSimulation/Event
git remote add hatakeyamak https://github.com/hatakeyamak/cmssw.git
git fetch hatakeyamak
git cherry-pick 0cf67551731c80dc85130e4b8ec73c8f44d53cb0

git clone https://github.com/missirol/JMETriggerAnalysis.git -o missirol -b phase2
scram b
```

### Instructions to generate configuration file(s) for HLT Phase-2 reconstruction:

* create configuration file to run TRK(v06)+PF+JME HLT-like reconstruction on RAW:
```
cmsDriver.py step3 \
  --geometry Extended2026D49 --era Phase2C9 \
  --conditions auto:phase2_realistic_T15 \
  --processName RECO2 \
  --step RAW2DIGI,RECO \
  --eventcontent RECO \
  --datatier RECO \
  --filein /store/mc/Phase2HLTTDRWinter20DIGI/QCD_Pt-15to3000_TuneCP5_Flat_14TeV-pythia8/GEN-SIM-DIGI-RAW/PU200_castor_110X_mcRun4_realistic_v3-v2/10000/05BFAD3E-3F91-1843-ABA2-2040324C7567.root \
  --mc \
  --nThreads 4 \
  --nStreams 4 \
  --no_exec \
  -n 10 \
  --customise SLHCUpgradeSimulations/Configuration/aging.customise_aging_1000,Configuration/DataProcessing/Utils.addMonitoring \
  --customise JMETriggerAnalysis/Common/hltPhase2_TRKv06.customize_hltPhase2_TRKv06 \
  --customise JMETriggerAnalysis/Common/hltPhase2_JME.customize_hltPhase2_JME \
  --customise_commands 'process.schedule.remove(process.RECOoutput_step)\ndel process.RECOoutput\ndel process.RECOoutput_step\n' \
  --python_filename hltPhase2_TRKv06_cfg.py
```

### Notes

 * [HLT Phase-2 Twiki / MC samples](https://twiki.cern.ch/twiki/bin/viewauth/CMS/HighLevelTriggerPhase2#MC_samples)
 * DAS query for Phase-2 MC samples (RAW):
```
dasgoclient --query="dataset dataset=/*/Phase2HLTTDRWinter20*/*RAW* status=VALID"
```
