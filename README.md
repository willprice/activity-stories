# EPIC-KITCHENS Activity Stories

Labels for activity stories used in the paper: 

Price et al (2022) [UnweaveNet: Unweaving Activity Stories](https://arxiv.org/abs/2112.10194), IEEE/CVF Computer Vision and Pattern Recognition (CVPR).

link available with camera ready submission for the paper,

## Citation

When using these labels, kindly reference:
```
@inproceedings{price2022UnweaveNet,
   title={UnweaveNet: Unweaving Activity Stories},
   author={Price, Will and Vondrick, Carl and Damen, Dima},
           booktitle={IEEE/CVF Computer Vision and Pattern Recognition (CVPR)},
           year={2022}
} 
```

## Videos

Labels below are for part of the [EPIC-KITCHENS-100 videos](https://data.bris.ac.uk/data/dataset/2g1n6qdydwa9u22shpxqzp0t8m). Videos were re-encoded to a fixed 30 FPS before frames aare extracted. All frame indices in the labels released here assume this.

## Activity Story Labels

All data is provided as a single [pickled](https://docs.python.org/3/library/pickle.html) [pandas dataframe](https://pandas.pydata.org/docs/reference/api/pandas.DataFrame.html) named `stories.pkl`.

## Example usage:
```python
import pandas as pd
df = pd.read_pickle('stories.pkl')
print(df.head())
```

Assuming a frame data loader `load_frames(video_id, frame_idxs)`, you can reconstruct the story like so:

```python
row = df.iloc[0]  # picking an arbitrary row as an example
threads = []
for thread_clip_frame_idxs in row['clip_frame_idxs']:
  frames = load_frames(row['video_id'], frame_idxs.flatten())
  frames = frames.reshape(frame_idxs.shape + frame_idxs.shape[1:])
  threads.append(frames)

story = np.stack([
  threads[thread_idx][thread_clip_idx]
  for thread_idx, thread_clip_idx in row['thread_clip_idxs']]
)
```

## Schema

| Column             | Description                                                                                                                                                          |
|--------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `video_id`         | The unique ID of the full video from EPIC-KITCHENS-100                                                                                                               |
| `clip_frame_idxs`  | A 2D array that describes the frame indices of each clip forming a thread. This can be indexed like so: `clip_frame_idxs[thread_idx][clip_idx] -> [video_frame_idx]` |
| `thread_clip_idxs` | A 2D array that describes how the clips are woven to form the story: `thread_clip_idxs[story_clip_idx] -> [thread_idx, thread_clip_idx]`                             |
| `id`               | A unique identifier for the story                                                                                                                                    |
| `split`            | One of `train`/`val`/`test` indicating the split the story belongs to                                                                                                |


