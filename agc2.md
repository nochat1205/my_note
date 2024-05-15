



### InputVolumeController

##### Functionality功能性

1. **Initial Gain Setting:初始增益设置：**
   - The input volume controller first sets the gain level of the incoming audio to bring it into an optimal range for processing. This helps in normalizing the audio input from various sources that may have different volume levels.输入音量控制器首先设置输入音频的增益级别，使其进入最佳处理范围。这有助于规范来自可能具有不同音量级别的各种源的音频输入。
2. **Pre-processing Adjustment:预处理调整：**
   - Before the audio signal is processed by other components (like noise reduction, echo cancellation, or other AGC components), the input volume controller adjusts the signal to a suitable level. This ensures that these components can perform optimally without being affected by too low or too high initial volumes.在音频信号被其他组件（如降噪、回声消除或其他 AGC 组件）处理之前，输入音量控制器会将信号调整到合适的水平。这确保了这些组件可以发挥最佳性能，而不会受到初始体积过低或过高的影响。
3. **Dynamic Response:动态响应：**
   - In some systems, the input volume controller may also dynamically adjust the gain in response to changing conditions in the input signal. For instance, if a person suddenly starts speaking louder, the controller can reduce the gain to keep the output level consistent.在一些系统中，输入音量控制器还可以响应于输入信号中的变化条件动态地调整增益。例如，如果一个人突然开始大声说话，控制器可以降低增益以保持输出电平一致。





The `Config` structure in the provided snippet is designed to configure the behavior of an audio processing system, such as an `InputVolumeController`. Each parameter in this configuration structure plays a specific role in managing how audio input is processed, particularly in handling issues like clipping, volume adjustments, and the integration of speech detection functionalities. Here's a breakdown of what each parameter does:
提供的代码段中的 `Config` 结构旨在配置音频处理系统的行为，例如 `InputVolumeController` .此配置结构中的每个参数在管理音频输入的处理方式方面都扮演着特定的角色，特别是在处理削波、音量调整和语音检测功能集成等问题时。以下是每个参数的作用的细分：

1. **`min_input_volume`**:
   - **Purpose**: Sets the minimum input volume level that can be recommended by the controller.
     用途：设置控制器可以推荐的最小输入音量级别。
   - **Context**: This prevents the system from setting the volume too low, which could render the audio inaudible, except when the applied input volume is zero (e.g., during startup or special modes).
     上下文：这可以防止系统将音量设置得太低，这可能会导致音频听不见，除非应用的输入音量为零（例如，在启动或特殊模式下）。
2. **`clipped_level_min`**:
   - **Purpose**: Specifies the lowest input volume level to be applied in response to detected clipping.
     用途：指定为响应检测到的削波而应用的最低输入音量级别。
   - **Context**: Ensures that when clipping is detected, the volume won't be reduced below this level, balancing between reducing distortion and maintaining audible volume.
     上下文：确保在检测到削波时，音量不会降低到此级别以下，从而在减少失真和保持可听音量之间取得平衡。
3. **`clipped_level_step`**:
   - **Purpose**: Defines the decrement in volume level with every detected clipping event.
     用途：定义每个检测到的削波事件的音量级递减。
   - **Context**: Allows the system to reduce the volume step-by-step to mitigate clipping without drastically dropping the volume in a single adjustment.
     上下文：允许系统逐步降低音量以减轻削波，而不会在一次调整中大幅降低音量。
4. **`clipped_ratio_threshold`**:
   - **Purpose**: Determines the proportion of clipped samples required to declare a clipping event.
     用途：确定声明剪切事件所需的剪裁样本的比例。
   - **Context**: This threshold helps in identifying significant clipping that necessitates volume adjustments.
     上下文：此阈值有助于识别需要调整音量的重大削波。
5. **`clipped_wait_frames`**:
   - **Purpose**: The number of frames to wait after a clipping event before checking for clipping again.
     用途：剪切事件后在再次检查剪切之前要等待的帧数。
   - **Context**: Prevents frequent toggling of volume adjustments by providing a cooldown period after making a clipping-based volume change.
     上下文：通过在进行基于剪裁的音量更改后提供冷却时间来防止频繁切换音量调整。
6. **`enable_clipping_predictor`**:
   - **Purpose**: A boolean to enable or disable the clipping prediction functionality.
     用途：用于启用或禁用剪裁预测功能的布尔值。
   - **Context**: Clipping predictors can anticipate potential clipping and preemptively adjust the volume, enhancing the responsiveness of the system.
     上下文：削波预测器可以预测潜在的削波并先发制人地调整音量，从而增强系统的响应能力。
7. **`target_range_min_dbfs`** and **`target_range_max_dbfs`**:
    `target_range_min_dbfs` 以及 `target_range_max_dbfs` ：
   - **Purpose**: Defines the target range for speech levels in dBFS. Adjustments based on speech levels are skipped if the speech level is within this range.
     用途：定义语音电平的目标范围（以 dBFS 为单位）。如果语音级别在此范围内，则跳过基于语音级别的调整。
   - **Context**: Ensures the system doesn't make unnecessary adjustments when the speech level is already optimal, focusing adjustments on scenarios where the speech level is too high or too low.
     上下文：确保系统在语音级别已经达到最佳状态时不会进行不必要的调整，将调整重点放在语音级别过高或过低的场景中。
8. **`update_input_volume_wait_frames`**:
   - **Purpose**: Sets the number of frames to wait between input volume updates recommended by the controller.
     用途：设置控制器建议的输入卷更新之间要等待的帧数。
   - **Context**: Helps in stabilizing the input volume by preventing too frequent changes, which could lead to erratic audio output.
     上下文：通过防止过于频繁的更改来帮助稳定输入音量，这可能会导致音频输出不稳定。
9. **`speech_probability_threshold`**:
   - **Purpose**: The threshold below which speech probabilities are considered as silence.
     目的：低于该阈值，语音概率被视为沉默。
   - **Context**: Assists in distinguishing between speech and non-speech segments of audio, aiding in more targeted volume adjustments.
     上下文：有助于区分音频的语音和非语音片段，有助于更有针对性地调整音量。
10. **`speech_ratio_threshold`**:
    - **Purpose**: The minimum ratio of frames that must contain speech (above the speech probability threshold) for volume updates to be enacted.
      用途：必须包含语音（高于语音概率阈值）的帧的最小比率，以便进行音量更新。
    - **Context**: Ensures that volume adjustments are only made when there is a significant presence of speech, avoiding unnecessary changes during largely non-speech segments.
      上下文：确保仅在有大量语音存在时进行音量调整，避免在大部分非语音片段中进行不必要的更改。

This configuration structure allows for detailed customization of the audio controller's behavior, making it adaptable to various environments and usage scenarios, such as VoIP communications, broadcasting, or any application where managing audio input quality is crucial.
此配置结构允许对音频控制器的行为进行详细自定义，使其能够适应各种环境和使用场景，例如 VoIP 通信、广播或管理音频输入质量至关重要的任何应用程序。







### process

> agc2 处理流程.



###### 组件:

speech_level_estimator: db估计器

saturation_protector: 饱和保护器。 分析峰值水平并建议余量, 减少剪辑的机会。



###### 流程:

1. 输入:
   
   1. speech_probability
   2. input_volume_changed
   3. audio_data
   
2. 初始化:

   1. if input_volume_changed

      speech_level_estimator.reset

      saturation_protector.reset

   2. audio_data to float _frame

3. Compute speech probability.

   speech_probability = vad_->Analyze(float_frame); TODO: GRU unit

4. Compute audio, noise and speech levels.

   audio_levels: [float_frame]=>{rms, peak} dbfs

5. noise_level_estimator: noise_level_estimator_->Analyze(float_frame)

   $frame\_energy = \sum_{x\in float\_frame}x^2$

   in period: $preliminary\_noise\_energy = min(frame\_energy)$ // 单调递减

   快速衰减, 缓慢启动:

    $noise\_energy = min( Weighted\_mean(noise\_energy, preliminary\_noise\_energy), preliminary\_noise\_energy)$,

   ```
   if full period observed(counter == 0):
   	noise_energy_ = SmoothNoiseFloorEstimate(
             /*current_estimate=*/noise_energy_,
             /*new_estimate=*/preliminary_noise_energy_);
         // 新的观察周期 重新估计 preliminary_noise_energy_
         counter_ = kUpdatePeriodNumFrames;
         preliminary_noise_energy_set_ = false;
   else if first_period:
         // While analyzing the signal during the initial period, continuously
         // update the estimated noise energy, which is monotonic.
         noise_energy_ = preliminary_noise_energy_;
         counter_--;
   else:
         // During the observation period it's only allowed to lower the energy.
         noise_energy_ = std::min(noise_energy_, preliminary_noise_energy_);
         counter_--;
   ```

   noise_energy to dbfs.

6. speech_level_estimator_ = speech_level_estimator_->Update(

   ​    audio_levels, *speech_probability);

   



sound -> 

