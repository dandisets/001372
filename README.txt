A MULTIMODAL MACAQUE TEMPLATE

The template of the paper "A multimodal macaque template for aligning ex vivo MRI and microscopy"
(Gonzales et al.): a symmetric population template of the macaque brain, in the symmetric
stereotaxic frame of NMT v2, with MRI channels and a blockface channel. The code that makes and uses
these files, with its documentation, is at https://github.com/lincbrain/fiber_template

WHAT IS HERE

Every file of the template is under derivatives/template/ and starts with template_. res-<x> gives
the grid of the file, desc-<method> what it holds, and from-<a>_to-<b> the direction of a transform.
A label file has a key beside it, with the same name ending in .tsv, with the columns index and
name. Open any .nii.gz file in a viewer such as FSLeyes or ITK-SNAP.

    anat/
      template_res-250um_desc-MSME_T2w.nii.gz
          the T2-weighted channel
    dwi/
      template_res-250um_desc-mean_b0.nii.gz
          the b0
      template_res-250um_desc-DTI_FA.nii.gz
          the fractional anisotropy
      template_res-250um_desc-DTI_MD.nii.gz
          the mean diffusivity
      template_res-250um_desc-DTI_DEC.nii.gz
          the principal diffusion direction, as direction-encoded color
      template_res-250um_desc-DTI_tensor.nii.gz
          the diffusion tensor
      template_res-250um_desc-SANDI_frac1.nii.gz
          the soma fraction, from SANDI (Soma And Neurite Density Imaging)
      template_res-250um_desc-SANDI_frac2.nii.gz
          the neurite fraction, from SANDI
    labels/
      template_res-250um_desc-brain_mask.nii.gz
          the brain
      template_res-250um_desc-FA025_wmmask.nii.gz
          the white matter (FA over 0.25)
      template_res-250um_desc-regions_dseg.nii.gz, with its .tsv key
          left, right and cerebellum
    micr/
      template_res-150um_desc-blockfacergb_photo.nii.gz
          the blockface channel, in color, at the blockface template's own resolution, 0.15 mm, in
          the template's frame: 427 x 534 x 320 voxels over the box of the 0.25 mm files
      template_res-150um_desc-photographedfraction_photo.nii.gz
          the photographed share of each voxel
      template_res-150um_desc-synthesized_mask.nii.gz
          the predicted tissue
    xfms/
      template_from-template_to-NMT2sym_xfm.nii.gz
      template_from-NMT2sym_to-template_xfm.nii.gz
          from the template into NMT v2 (symmetric), and back
      template_from-template_to-INIA19_xfm.nii.gz
      template_from-INIA19_to-template_xfm.nii.gz
          from the template into INIA19, and back
      template_from-template_to-CIVMR_xfm.nii.gz
      template_from-CIVMR_to-template_xfm.nii.gz
          from the template into CIVM-R, and back
    dataset_description.json
          the name, authors and license of this dataset

The other maps of the tensor (its eigenvalues, its mode and its eigenvectors) follow from
desc-DTI_tensor and are not given as files. The brain mask of the blockface channel is the nonzero
voxels of its color image: the color is zero outside it.

HOW THE TEMPLATE WAS MADE

    The MRI channels were built from the ex vivo MRI of four Macaca fascicularis brains, MF1 to MF4
    in the paper, and their mirror images. Part of their preprocessed MRI is public in DANDI
    dataset 001372, from Gong et al. 2026 (doi:10.64898/2026.02.18.705310), where MF1 to MF4 are
    sub-M1 to sub-M4 in that order.
    The blockface channel is the average of the blockface photographs of the 11 brains photographed
    at every section, with few interpolated sections and steady lighting, 9 Macaca mulatta (MM2,
    MM3, MM5 to MM10, MM15) and 2 Macaca fascicularis (MF5, MF7), and their mirror images. It was
    refined at 0.15 mm from an earlier average of all 21 photographed brains (MM1 to MM15, MF5 to
    MF10), whose frame it keeps. No brain was photographed to its caudal end, so the blockface
    channel includes predicted tissue there: desc-synthesized_mask marks it.

THE TRANSFORMS

    Each of the six files in xfms/ is one complete transform, an ITK displacement field that holds
    the linear and the nonlinear parts. template_from-template_to-<atlas>_xfm.nii.gz moves an image
    from the template into <atlas> (NMT2sym, INIA19 or CIVMR), and
    template_from-<atlas>_to-template_xfm.nii.gz moves an image the other way. Apply one alone, with
    one -t. For example, from derivatives/template/xfms/, with NMT v2:

        antsApplyTransforms -d 3 -i <image in the template> -r NMT_v2.0_sym_SS.nii.gz \
            -o <image in NMT2sym> -t template_from-template_to-NMT2sym_xfm.nii.gz
        antsApplyTransforms -d 3 -i <image in NMT2sym> \
            -r ../anat/template_res-250um_desc-MSME_T2w.nii.gz \
            -o <image in the template> -t template_from-NMT2sym_to-template_xfm.nii.gz

    The reference (-r) sets the grid of the output. Into a published template, give one of its own
    images: for NMT v2, NMT_v2.0_sym_SS.nii.gz or another image of its distribution, on the same
    0.25 mm grid; for INIA19, its T1 (0.5 mm); for CIVM-R, civm_rhesus_v1_b0.nii.gz (0.15 mm). Into
    the template, give any of its res-250um images: they all share one 0.25 mm grid. For a label
    image, add -n GenericLabel; the default, linear interpolation, is for intensity images.

    Only the transforms are here. The images of the other templates are not: download those
    yourself. CIVM-R's license does not allow its images to be redistributed.

WHAT IS NOT HERE

    This dataset holds the template only. Each brain's own files (its MRI results, transforms,
    blockface volumes and labels) are not part of it.
    The histology is in LINC dataset 000003.
    Blockface and histology data and anything computed from them (the data of Suzanne Haber's
    laboratory) stay local. The one exception is the template's blockface channel (micr/), an
    average of 11 brains; no section photograph is uploaded.
    The published templates (NMT v2, INIA19, CIVM-R) are not ours to redistribute: only the
    transforms to and from them are here.
    The transforms between the blockface template and the template, which are computed from
    blockface data, stay local, and so do the blockface template in its own space and the
    blockface channel on the template's 0.25 mm grid.
    The trained networks (the brain masks, the fiducial markers and the synthesized tissue) are
    in the code repository, under models/.

HOW TO CITE

    Cite the paper, "A multimodal macaque template for aligning ex vivo MRI and microscopy"
    (Gonzales et al.), and this dataset, whose name and authors are in dataset_description.json.

LICENSE

    Creative Commons Attribution 4.0 (CC BY 4.0).

CONTACT

    Ricardo A. Gonzales, Athinoula A. Martinos Center for Biomedical Imaging, Massachusetts General
    Hospital (rgonzalesvera@mgh.harvard.edu), or the code repository's issues page:
    https://github.com/lincbrain/fiber_template/issues
