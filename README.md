# omg_vpcc

Executable files for the occupancy map guided fast Video-based Point Cloud Compression (OMG_VPCC) method which is submitted to TCSVT. Provided by Jian Xiong (jxiong AT njupt Dot edu Dot cn).


Four files are included as 

1. TAppEncoderStatic: The executable file for encoding the geometry and attribute videos. The proposed occupancy map guided fast Video-based Point Cloud Compression (OMG_VPCC) method is integrated into the file.
2. TAppEncoderStatic_occ: The executable file for encoding the occupancy maps. 
3. PccAppEncoder: The compiled TMC2 file, in which TAppEncoderStatic and TAppEncoderStatic_occ are called in different cases.
4. _exePC.sh: A batch file for test the five typical point clouds, including queen, loot, longdress, redandblack, and soldier. The detailed test conditions are also included in this file.


Runtime environment: Linux

The steps are as follows:
1.	put the files “TAppEncoderStatic” and “TAppEncoderStatic_occ” into the file folder “…\tmc2\HM-16.18+SCM-8.7\bin”.
2.	Put the file “PccAppEncoder” into “… tmc2\mpeg-pcc-tmc2\bin”.

More details:

Since the non-normative 3D MVP is set to true in V-PCC Common Test Condition, the dependencies should be updated in the HM reference software as follows

-	cd /mnt/d/aSourceCode/Point_Cloud/tmc2/mpeg-pcc-tmc2/dependencies/hm-modification
-	unix2dos ee2.1-3dme-3dmc.patch
-	cd /mnt/d/aSourceCode/Point_Cloud/tmc2/HM-16.18+SCM-8.7
-	patch --binary -p0 < /mnt/d/aSourceCode/Point_Cloud/tmc2/mpeg-pcc-tmc2/dependencies/hm-modification/ee2.1-3dme-3dmc.patch

Note that, three additional files, including blockToPatch.txt, occupancyMap.txt, and patchInfo.txt, are used in non-normative 3D MVP. However, these files are not available before encoding the occupancy maps.
Therefore, the HM software is compiled twice, by setting the macro PATCH_BASED_MVP_NON_NORMATIVE to 0 and 1, respectively.

For the macro set to 0, the compiled file is used to encode occupancy maps.
We rename the compiled file “TAppEncoderStatic” as “TAppEncoderStatic_occ”.
For the macro set to 1, the compiled file “TAppEncoderStatic” is used to encode the geometry and attribute videos.

Then, to call the different coding files, the 355th line in file “PCCVideoEncoder.h” needs to be updated as follows.
The source code before:
---------------------------------------------------------------------------
        cmd << encoderPath << " -c " << encoderConfig << " -i " << srcYuvFileName << " --InputBitDepth=" << depth
          << " --InputChromaFormat=" << format << " --FrameRate=30 "
          << " --FrameSkip=0 "
          << " --SourceWidth=" << width << " --SourceHeight=" << height << " --ConformanceWindowMode=1 "
          << " --FramesToBeEncoded=" << frameCount << " --BitstreamFile=" << binFileName
          << " --ReconFile=" << recYuvFileName << " --QP=" << qp;
---------------------------------------------------------------------------
The source code after changing:

---------------------------------------------------------------------------
      if(use3dmv){    
        cmd << encoderPath;
      }
      else{
        cmd << encoderPath << "_occ";   
      }    
      cmd << " -c " << encoderConfig << " -i " << srcYuvFileName << " --InputBitDepth=" << depth
          << " --InputChromaFormat=" << format << " --FrameRate=30 "
          << " --FrameSkip=0 "
          << " --SourceWidth=" << width << " --SourceHeight=" << height << " --ConformanceWindowMode=1 "
          << " --FramesToBeEncoded=" << frameCount << " --BitstreamFile=" << binFileName
          << " --ReconFile=" << recYuvFileName << " --QP=" << qp;
---------------------------------------------------------------------------

The compiled TMC2 file is “PccAppEncoder”.
