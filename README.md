<h1 align="center">Dynamic Routing Tutorial</h1>  
<h1 align="center">Allen Institute / Neural Dynamics</h1>  

## Project overview
The Dynamic Routing project aims to uncover the neural mechanisms of flexible decision making. We train mice to perform a visual–auditory switching task in which the rewarded sensory modality alternates within a session, requiring mice to dynamically route sensory information to appropriate motor outputs depending on behavioral context. While mice perform this task, we record from neurons across the mouse brain to identify the neural correlates of flexible sensory-motor associations and to understand how context representations are generated, maintained, and used to guide behavior.

## Recording strategy
We insert up to six Neuropixels 1.0 (NP 1.0) probes simultaneously across the mouse brain using the **SHIELD implant** ([Bennett et al., *Neuron* 2024](https://doi.org/10.1016/j.neuron.2024.06.015)), a chronic implant system that enables repeatable, multi-probe access to dorsal cortex and underlying structures. Probes are inserted at varying angles and anterior-posterior positions to achieve broad coverage spanning cortical, thalamic, basal ganglia, and midbrain structures (among others). This approach allows us to simultaneously sample the activity of hundreds of neurons across functionally distinct brain regions while the mouse performs our switching task.

---
| ![SHIELD implant with multiple Neuropixels probes](code/images/SHIELD_diagram.png) | ![Brain-wide coverage of probe insertions](code/images/brainwide_recording.png) |
|---|---|
| *Diagram of SHIELD implant with multiple Neuropixels 1.0 probes.* | *Probe trajectories across mice and sessions showing dense coverage of left hemisphere.* |
---

## Task

Mice perform a **visual-auditory context-switching task** in which the rewarded stimulus modality alternates across blocks within each session.

### Stimuli and Reward Contingencies

Each trial presents one of four stimuli: a visual target (VIS1), a visual non-target (VIS2), an auditory target (AUD1), or an auditory non-target (AUD2). Trials are organized into alternating **auditory-rewarded (A)** and **visual-rewarded (V)** blocks. Within each block, only one target stimulus is rewarded (Figure below, panel **a**):

- **Auditory context (A):** licking in response to AUD1 earns a water reward; licking to VIS1 is a false alarm.
- **Visual context (V):** licking in response to VIS1 earns a water reward; licking to AUD1 a false alarm.

Responses to either non-target stimulus (AUD2, VIS2) are never rewarded regardless of context.

### Session and Block Structure

A session lasts approximately 60 minutes and consists of six ~10-minute blocks alternating between auditory- and visual-rewarded contexts (Figure below, panel **b**). **Context is not explicitly cued on every trial** — the mouse must infer and maintain its representation of the current block from trial outcomes.

Block transitions are signaled by **5 consecutive presentations of the newly rewarded target stimulus** (purple in panel **b**). If the mouse does not earn a contingent reward by licking during these trials, a non-contingent reward is given at the end of the response window. Following these trials, all four stimuli are presented in pseudorandom interleaved order for the remainder of the block.

### Trial Structure

Each trial begins with a **quiescent period** (1.5 s before stimulus onset) during which licking resets the trial. The stimulus is presented for 0.5 s, followed by a **response window** from 0.1–1.0 s post-stimulus onset. The trial concludes with an **inter-trial interval (ITI)** of 3–7.5 s before the next trial begins.

![Task diagram showing reward contingencies for auditory and visual context blocks (panel a) and the nested timeline of a session, block, and trial (panel b)](code/images/DR_task_diagram.png)
*__a__, Reward contingencies for the auditory (A) and visual (V) contexts. Only the target of the currently rewarded modality yields reward. __b__, Nested timeline of a session (six alternating 10-minute blocks), a block (five rewarded target cue trials followed by pseudo-randomly interleaved stimuli), and a trial (quiescent period, stimulus, response window, ITI).*


## Data in this capsule

This dataset comprises 12 Neuropixels recording sessions, stored in a publicly accessible Amazon S3 bucket. 

Each attached asset contains:
- an .nwb file saved as a .zarr folder
- `nwb_contents.json` detailing the internal paths within each .hdf5 file, e.g.:
    ```
    [
        "/intervals/trials",
        "/processing/behavior/running_speed",
        "/units"
    ]   
    ```
- AIND metadata `.json` files


### Summary

All animals successfully switched between contexts, as demonstrated by high d' values for the rewarded modality and suppressed responses to the non-rewarded modality. 

Some of the mouse lines express channelrhodopsin (ChR2) in specific inhibitory interneuron classes (Pvalb, Sst, Vip, or all GABAergic via VGAT), enabling optotagging of those cell types. One mouse (668755) is wild-type.

---

## Session Structure (Epochs)

Each recording session follows a standardized sequence of epochs:

1. **RFMapping** (~15 min) — Receptive field mapping with visual and auditory stimuli
2. **OptoTagging** (~3–6 min) — Optogenetic identification of genetically-defined neurons (pre-task)
3. **Spontaneous** (~10 min) — Spontaneous activity, no stimuli
4. **SpontaneousRewards** (~10 min) — Spontaneous activity with non-contingent rewards
5. **DynamicRouting1** (~60 min) — Main behavioral task
6. **SpontaneousRewards** (~10 min) — Post-task spontaneous with rewards
7. **OptoTagging** (~3–6 min) — Post-task optotagging (some sessions)
8. **Spontaneous** (~10 min) — Post-task spontaneous (some sessions)

---

### Context Blocks and Rule Switching

The session is divided into **6 blocks** (indices 0–5), alternating between two rewarded-modality contexts:

- **Visual context** (`rewarded_modality = "vis"`): The mouse must lick to visual targets (`vis1` → go) and withhold licking to all other stimuli (including auditory targets `aud+` → no-go).
- **Auditory context** (`rewarded_modality = "aud"`): The mouse must lick to auditory targets (`aud1` → go) and withhold licking to all other stimuli (including visual targets `vis+` → no-go).

Some sessions begin with visual blocks first, others with auditory blocks first. Each modality context has 3 blocks per session.

---

## Trial Structure

Each trial proceeds through the following phases:

1. **Quiescent period** (`quiescent_start_time` → `quiescent_stop_time`): The mouse must remain still (no licking) before a stimulus is presented. Violations restart the quiescent period.
2. **Stimulus presentation** (`stim_start_time` → `stim_stop_time`): A visual grating, auditory stimulus, or catch (blank) is presented.
3. **Response window** (`response_window_start_time` → `response_window_stop_time`): The mouse can lick to report detection. A lick within this window on a go trial is a **hit**; on a no-go trial it is a **false alarm**.
4. **Post-response window** (`post_response_window_start_time` → `post_response_window_stop_time`): Brief post-response period.
5. **Reward** (`reward_time`, if applicable): Water reward delivered on correct go responses (hits) and on some instruction/auto-reward trials.

### Trial Types

| Trial Type | Description |
|------------|-------------|
| **Go** | Target stimulus in the currently rewarded modality; lick = hit, no lick = miss |
| **No-go** | Non-target stimulus, or target in non-rewarded modality; lick = false alarm, no lick = correct reject |
| **Catch** | No stimulus; used to measure baseline lick rate |
| **Instruction** | Auto-rewarded trials (30 per session) at block transitions to cue the new rule |

Additional trial flags: `is_repeat` (repeated after a miss), `is_opto` (optogenetic stimulation applied — 0 opto trials in these task sessions), `is_contingent_reward` / `is_noncontingent_reward`.

---

## Performance Summary

Key observations:
- All mice showed **strong context-dependent discrimination**: high d' for the rewarded modality and near-zero or negative d' for the non-rewarded modality, demonstrating successful task switching.
- **Cross-modal d'** (measuring discrimination between the target of the rewarded modality vs. the target of the non-rewarded modality) was consistently positive (1.75–3.69), confirming that animals selectively responded to the correct modality.
- Hit rates were uniformly high (0.82–0.99). False alarm rates were low to moderate (0.03–0.30), varying across animals and contexts.

---

## Electrophysiology: Recorded Areas (QC-Passing Units Only)

Units were filtered by `is_qc_pass = true`. Each session used 5–6 Neuropixels probes.

### Commonly Recorded Areas Across Sessions

The most frequently recorded areas with QC-passing units include:

- **Frontal cortex**: MOs (secondary motor), FRP (frontal pole), ACAd/ACAv (anterior cingulate), MOp (primary motor)
- **Somatosensory cortex**: SSp (primary), SSs (supplemental)
- **Prefrontal / orbitofrontal**: ORBvl, ORBl, ORBm, ILA (infralimbic), PL (prelimbic)
- **Visual cortex**: VISp, VISal, VISam, VISrl, VISli, VISpm
- **Auditory cortex**: AUDp (primary), AUDd (dorsal), AUDv (ventral), AUDpo (posterior)
- **Temporal association**: TEa, ECT (ectorhinal), PERI (perirhinal)
- **Hippocampus**: CA1, CA2, CA3, DG
- **Lateral septum**: LSr, LSc
- **Striatum**: CP (caudoputamen)
- **Thalamus**: AD (anterodorsal), AV (anteroventral)
- **Midbrain / superior colliculus**: SCig, SCiw, SCsg, SCop, SCdg, SCzo, SCdw
- **Olfactory**: AON, OLF, TTd, MOB, DP
- **Other**: GU (gustatory), RSPv/RSPd (retrosplenial), PAG, PPT, HPF, MB

---

# Resources: 

### Allen Institute / Neural Dynamics Data Portal 
Browse data visualizations for each session

[https://data.allenneuraldynamics-test.org/swdb/set?dataset=swdb_2026_dynamic_routing](https://data.allenneuraldynamics-test.org/swdb/set?dataset=swdb_2026_dynamic_routing)

### Summer Workshop on the Dynamic Brain Data Book 
For further background reading on the project and code tutorials, view the SWDB Data Book 

[Dynamic Routing Overview](https://allenswdb.github.io/physiology/ephys/dynamic-routing/dynamic-routing-background.html) 

### Allen Mouse Brain Atlas 
Confused about the structure acronyms? Check out the Brain Atlas to see the full name of each structure and where it is located in the brain. 

[https://atlas.brain-map.org/](https://atlas.brain-map.org/) 

### Neuroglancer 
Explore CCF brain structures in neuroglancer (requires google account to access) 

[Neuroglancer Link](https://spelunker.cave-explorer.org/#!middleauth+https://global.daf-apis.com/nglstate/api/v1/6082312788770816)



