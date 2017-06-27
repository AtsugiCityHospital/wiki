HCP_Pipelineの使い方
===
# 1.Pipelines-3.22.0/Examples/Scripts/SetUpHCPPipeline.shを設定


# 2.PreFreesuferBatch.shの設定

AvgrdcSTRING=“NONE"

MagnitudeInputName=“NONE"

PhaseInputName=“NONE"

TE=“NONE"

SpinEchoPhaseEncodeNegative="NONE"

SpinEchoPhaseEncodePositive="NONE"

DwellTime="NONE"

SEUnwarpDir="NONE"

TopupConfig=“${HCPPIPEDIR_Config}/b02b0.cnf” 

GEB0InputName="NONE"

## Templates
T1wTemplate="${HCPPIPEDIR_Templates}/MNI152_T1_1mm.nii.gz" #Hires T1w MNI template
T1wTemplateBrain="${HCPPIPEDIR_Templates}/MNI152_T1_1mm_brain.nii.gz" #Hires brain extracted MNI template
T1wTemplate2mm="${HCPPIPEDIR_Templates}/MNI152_T1_2mm.nii.gz" #Lowres T1w MNI template
T2wTemplate="${HCPPIPEDIR_Templates}/MNI152_T2_1mm.nii.gz" #Hires T2w MNI Template
T2wTemplateBrain="${HCPPIPEDIR_Templates}/MNI152_T2_1mm_brain.nii.gz" #Hires T2w brain extracted MNI Template
T2wTemplate2mm="${HCPPIPEDIR_Templates}/MNI152_T2_2mm.nii.gz" #Lowres T2w MNI Template
TemplateMask="${HCPPIPEDIR_Templates}/MNI152_T1_1mm_brain_mask.nii.gz" #Hires MNI brain mask template
Template2mmMask="${HCPPIPEDIR_Templates}/MNI152_T1_2mm_brain_mask_dil.nii.gz" #Lowres MNI brain mask template

T1wSampleSpacing="NONE"

T2wSampleSpacing="NONE"

UnwarpDir="NONE"

BrainSize="150"
FNIRTConfig="${HCPPIPEDIR_Config}/T1_2_MNI152_2mm.cnf"

GradientDistortionCoeffs="NONE"

# FreeSurferPipelineBatch.sh & PostFreeSurferPipelineBatch.sh
変更不要

