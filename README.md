# EPIC-KITCHENS Activity Stories

Data used in the paper [UnweaveNet: Unweaving Activity Stories](https://arxiv.org/abs/2112.10194).

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


## Notes

The original EPIC-KITCHENS-100 videos were encoded to a constant 30 FPS. All frame indices in the annotations assume this.
