---
title: 在react native中使用spleeter实现人声伴奏分离
abbrlink: a2d2f901
date: 2025-11-26 15:06:55
tags:
  - react native
  - android
categories: 前端
---

## TensorFlow Lite技术方案简介
TensorFlow Lite是Google推出的轻量级机器学习框架，专为移动和嵌入式设备设计。它通过以下特点满足端侧设备的需求：

- 轻量高效：核心运行时库在32位Android平台上仅约100KB，即使包含常用算子也控制在300KB左右，远小于标准TensorFlow。
- 跨平台支持：支持Android、iOS、嵌入式Linux及微控制器(MCU)等多种平台。
- 硬件加速：能够利用CPU、GPU和DSP等硬件加速器提升模型推理速度。

将Spleeter的TensorFlow模型转换为TFLite格式后，就能在React Native Android应用中高效运行人声分离任务。
<!-- more -->

## 集成步骤详解
1. 从[https://github.com/jinay1991/spleeter/releases](https://github.com/jinay1991/spleeter/releases)链接下载别人已经转换好的TFLite模型文件（.tflite）。后续可以考虑自己训练模型并转换为TFLite格式。
Spleeter常见的模型有：
- 2stems模型：分离人声和伴奏。
- 4stems模型：分离人声、鼓声、贝斯和其他乐器。
- 5stems模型：在4stems基础上增加钢琴声分离。
这里我们选择2stems模型。
2. 将下载的模型文件放置在React Native项目的`android/app/src/main/assets/tensorFlow/`目录下。如果没有该目录，需要手动创建。
3. 在React Native项目的`android/app/build.gradle`文件中添加以下依赖：
```
implementation 'org.tensorflow:tensorflow-lite:2.13.0'
implementation 'org.tensorflow:tensorflow-lite-support:0.4.4'
implementation 'org.tensorflow:tensorflow-lite-api:2.13.0'
implementation 'org.tensorflow:tensorflow-lite-select-tf-ops:2.13.0'
implementation('org.tensorflow:tensorflow-lite-support:0.4.4') { 
    changing = true
    exclude group: 'com.android.support'
}
```
因为处理音频需要用到ffmpeg，而且也需要预留在react native 前端预留使用ffmpeg，所以安装react native 的 react-native-ffmpeg-kit库：
```
yarn add react-native-ffmpeg-kit
// Add the following script to download the file from the cloud
afterEvaluate {
    def aarUrl = 'https://github.com/NooruddinLakhani/ffmpeg-kit-full-gpl/releases/download/v1.0.0/ffmpeg-kit-full-gpl.aar'
    def aarFile = file("${rootDir}/libs/ffmpeg-kit-full-gpl.aar")

    tasks.register("downloadAar") {
        doLast {
             if (!aarFile.parentFile.exists()) {
                println "📁 Creating directory: ${aarFile.parentFile.absolutePath}"
                aarFile.parentFile.mkdirs()
            }
            if (!aarFile.exists()) {
                println "⏬ Downloading AAR from $aarUrl..."
                new URL(aarUrl).withInputStream { i ->
                    aarFile.withOutputStream { it << i }
                }
                println "✅ AAR downloaded to ${aarFile.absolutePath}"
            } else {
                println "ℹ️ AAR already exists at ${aarFile.absolutePath}"
            }
        }
    }

    // Make sure the AAR is downloaded before compilation begins
    preBuild.dependsOn("downloadAar")
}
```
根据react-native-ffmpeg-kit的文档，添加配置：
```
implementation(name: 'ffmpeg-kit-full-gpl', ext: 'aar')
```
同时需要在`android/libs`目录下添加`ffmpeg-kit-full-gpl.aar`文件。

4. 在React Native项目的`android/app/src/main/java/com/yugeaudiovisual/`新建`Logger.kt`文件：
```
// 日志工具类
package com.yugeaudiovisual

import android.os.Environment
import android.util.Log
import java.io.File
import java.io.FileWriter
import java.io.PrintWriter
import java.text.SimpleDateFormat
import java.util.*

class Logger(private val tag: String) {
    companion object {
        private const val LOG_DIR = "yugeApp/log"
        private val sdf = SimpleDateFormat("yyyy-MM-dd HH:mm:ss.SSS", Locale.getDefault())
    }

    fun writeLog(message: String) {
        try {
            // 确保目录存在
            val logDir = File(Environment.getExternalStoragePublicDirectory(Environment.DIRECTORY_DOWNLOADS), LOG_DIR)
            if (!logDir.exists()) {
                logDir.mkdirs()
            }

            val logFile = File(logDir, "audio_separation.log")
            val currentTime = sdf.format(Date())

            val logMessage = "$currentTime [$tag]: $message\n"

            // 使用追加模式写入日志
            PrintWriter(FileWriter(logFile, true)).use { writer ->
                writer.write(logMessage)
            }
            
            // 同时输出到 Logcat
            Log.d(tag, message)
        } catch (e: Exception) {
            Log.e(tag, "Failed to write log to file: ${e.message}")
            // 即使无法写入文件也尝试使用 Android 日志
            Log.d(tag, "LOG_FALLBACK: $message")
        }
    }

    fun writeErrorLog(message: String, throwable: Throwable? = null) {
        try {
            // 确保目录存在
            val logDir = File(Environment.getExternalStoragePublicDirectory(Environment.DIRECTORY_DOWNLOADS), LOG_DIR)
            if (!logDir.exists()) {
                logDir.mkdirs()
            }

            val logFile = File(logDir, "audio_separation_error.log")
            val currentTime = sdf.format(Date())

            val logMessage = StringBuilder()
            logMessage.append("$currentTime [$tag]: $message\n")
            
            if (throwable != null) {
                logMessage.append("Exception: ${throwable.javaClass.simpleName}: ${throwable.message}\n")
                throwable.stackTrace.forEach { 
                    logMessage.append("  at $it\n")
                }
            }

            // 使用追加模式写入日志
            PrintWriter(FileWriter(logFile, true)).use { writer ->
                writer.write(logMessage.toString())
            }
            
            // 同时输出到 Logcat
            if (throwable != null) {
                Log.e(tag, message, throwable)
            } else {
                Log.e(tag, message)
            }
        } catch (e: Exception) {
            Log.e(tag, "Failed to write error log to file: ${e.message}")
            // 即使无法写入文件也尝试使用 Android 日志
            if (throwable != null) {
                Log.e(tag, "LOG_FALLBACK: $message", throwable)
            } else {
                Log.e(tag, "LOG_FALLBACK: $message")
            }
        }
    }
}
```

新建`AudioSeparator.kt`文件：
```
// 音频分离
package com.yugeaudiovisual

import android.content.Context
import android.media.MediaMetadataRetriever
import android.os.Environment
import android.util.Log
import com.arthenica.ffmpegkit.FFmpegKit
import com.arthenica.ffmpegkit.ReturnCode
import org.tensorflow.lite.Interpreter
import org.tensorflow.lite.flex.FlexDelegate
import org.tensorflow.lite.support.common.FileUtil
import java.io.File
import java.nio.MappedByteBuffer
import java.nio.ByteBuffer
import java.nio.ByteOrder
import kotlin.math.min
import kotlin.math.max

class AudioSeparator(private val context: Context) {
    companion object {
        private const val TAG = "AudioSeparator"
        private const val SAMPLE_RATE = 44100
        private const val CHANNELS = 2
        private const val CHUNK_DURATION = 10 // 每段10秒
        private const val CHUNK_OVERLAP = 1 // 重叠1秒以改善拼接
        private const val FADE_DURATION_MS = 30 // 每个片段淡入/淡出时长（毫秒）
        private const val MAX_MODEL_OUTPUT = 882000 // 每段最大输出帧数 (44100 * 2 * 10)
    }

    private var interpreter: Interpreter? = null
    private val modelPath = "tensorFlow/2stems.tflite"
    private val logger = Logger(TAG)

    init {
        loadModel()
    }

    private fun loadModel() {
        try {
            val modelByteBuffer: MappedByteBuffer = FileUtil.loadMappedFile(context, modelPath)
            val options = Interpreter.Options()
            options.setNumThreads(4)
            
            val flexDelegate = FlexDelegate()
            options.addDelegate(flexDelegate)
            
            interpreter = Interpreter(modelByteBuffer, options)
            
            if (interpreter != null) {
                val inputCount = interpreter!!.getInputTensorCount()
                val outputCount = interpreter!!.getOutputTensorCount()
                
                for (i in 0 until inputCount) {
                    val inputTensor = interpreter!!.getInputTensor(i)
                    Log.d(TAG, "Input tensor $i: ${inputTensor.shape().contentToString()}")
                }
                
                for (i in 0 until outputCount) {
                    val outputTensor = interpreter!!.getOutputTensor(i)
                    Log.d(TAG, "Output tensor $i: ${outputTensor.shape().contentToString()}")
                }
            }
        } catch (e: Exception) {
            logger.writeErrorLog("Error loading 2stems model: ${e.message}", e)
            Log.e(TAG, "Error loading 2stems model: ${e.message}")
        }
    }

    fun separateAudio(
        inputFilePath: String,
        originalFileName: String,
        progressCallback: (Double, String) -> Unit,
        callback: (Result<Map<String, String>>) -> Unit
    ) {
        Thread {
            try {
                logger.writeLog("Starting audio separation process for file: $inputFilePath")
                // 将进度回调传递到处理流程
                val result = processAudioSeparation(inputFilePath, originalFileName, progressCallback)
                logger.writeLog("Audio separation process completed successfully")
                // 返回包含 vocals & accompaniment 路径的 map
                val map = HashMap<String, String>()
                map["vocals"] = result.first
                map["accompaniment"] = result.second
                callback(Result.success(map))
            } catch (e: Exception) {
                logger.writeErrorLog("Error separating audio: ${e.message}", e)
                Log.e(TAG, "Error separating audio: ${e.message}")
                callback(Result.failure(e))
            }
        }.start()
    }

    /**
     * 处理音频分离的主要函数 - 精确修复版本
     */
    private fun processAudioSeparation(inputFilePath: String, originalFileName: String, progressCallback: (Double, String) -> Unit): Pair<String, String> {
        var tempDir: File? = null

        // 在每次处理前清理 native 日志文件，避免日志无限增长
        try {
            val logDir = File(Environment.getExternalStoragePublicDirectory(Environment.DIRECTORY_DOWNLOADS), "yugeApp/log")
            if (logDir.exists()) {
                val logFile = File(logDir, "audio_separation.log")
                val errFile = File(logDir, "audio_separation_error.log")
                try { if (logFile.exists()) logFile.delete() } catch (e: Exception) { /* ignore */ }
                try { if (errFile.exists()) errFile.delete() } catch (e: Exception) { /* ignore */ }
            } else {
                // 尝试创建目录
                try { logDir.mkdirs() } catch (e: Exception) { /* ignore */ }
            }
        } catch (e: Exception) {
            logger.writeErrorLog("Failed to cleanup native log files before processing: ${e.message}", e)
        }
        
        try {
            val inputFile = File(inputFilePath)
            if (!inputFile.exists()) {
                val errorMsg = "Input file does not exist: $inputFilePath"
                logger.writeErrorLog(errorMsg)
                throw IllegalArgumentException(errorMsg)
            }

            // 获取音频信息
            val retriever = MediaMetadataRetriever()
            retriever.setDataSource(inputFilePath)
            val durationStr = retriever.extractMetadata(MediaMetadataRetriever.METADATA_KEY_DURATION)
            val durationMs = durationStr?.toLongOrNull() ?: 0
            val durationSeconds = (durationMs / 1000).toInt()
            retriever.release()
            
            logger.writeLog("Audio duration: ${durationMs}ms (${durationSeconds}s)")

            // 创建临时目录
            tempDir = File(context.cacheDir, "audio_separation_${System.currentTimeMillis()}")
            if (!tempDir.exists()) {
                tempDir.mkdirs()
            }

            // 使用原始文件名创建输出目录
            val fileNameWithoutExtension = File(originalFileName).nameWithoutExtension
            val outputDir = getOutputDirectory(fileNameWithoutExtension)
            
            if (!outputDir.exists()) {
                outputDir.mkdirs()
            }
            
            val vocalsOutputFile = File(outputDir, "vocals.wav")
            val accompanimentOutputFile = File(outputDir, "accompaniment.wav")

            // 清理之前的输出文件
            if (vocalsOutputFile.exists()) vocalsOutputFile.delete()
            if (accompanimentOutputFile.exists()) accompanimentOutputFile.delete()

            logger.writeLog("Output directory: ${outputDir.absolutePath}")

            // 使用精确的分段处理，并传入进度回调
            processWithPreciseChunks(inputFilePath, tempDir, vocalsOutputFile, accompanimentOutputFile, durationSeconds, progressCallback)

            // 验证输出文件时长
            val vocalsDuration = getAudioDuration(vocalsOutputFile.absolutePath)
            val accompanimentDuration = getAudioDuration(accompanimentOutputFile.absolutePath)
            
            logger.writeLog("Separation completed:")
            logger.writeLog("Vocals output: ${vocalsOutputFile.absolutePath} (${vocalsDuration}s)")
            logger.writeLog("Accompaniment output: ${accompanimentOutputFile.absolutePath} (${accompanimentDuration}s)")
            logger.writeLog("Original duration: ${durationSeconds}s")

            // 返回两个输出路径
            return Pair(vocalsOutputFile.absolutePath, accompanimentOutputFile.absolutePath)
        } finally {
            // 清理临时目录
            try {
                tempDir?.deleteRecursively()
                logger.writeLog("Temporary directory cleaned up successfully")
            } catch (e: Exception) {
                logger.writeErrorLog("Error cleaning up temporary directory: ${e.message}", e)
            }
        }
    }

    /**
     * 精确分段处理 - 核心修复
     */
    private fun processWithPreciseChunks(
        inputFilePath: String,
        tempDir: File,
        vocalsOutputFile: File,
        accompanimentOutputFile: File,
        durationSeconds: Int,
        progressCallback: (Double, String) -> Unit
    ) {
        logger.writeLog("Processing audio with precise chunked approach")
        
        val chunks = calculatePreciseChunks(durationSeconds)
        val chunkCount = chunks.size
        
        logger.writeLog("Processing audio in $chunkCount chunks")
        
        // 创建临时文件列表用于最终合并
        val vocalsChunkFiles = mutableListOf<String>()
        val accompanimentChunkFiles = mutableListOf<String>()
        
        try {
            // 处理每个音频块
            for (i in 0 until chunkCount) {
                val (startTime, chunkDuration) = chunks[i]
                
                logger.writeLog("Processing chunk $i: ${startTime}s-${startTime + chunkDuration}s (duration: ${chunkDuration}s)")

                // 精确提取音频块 - 使用更严格的参数
                val chunkFile = File(tempDir, "chunk_$i.wav")
                extractAudioChunkWithPrecision(inputFilePath, chunkFile.absolutePath, startTime, chunkDuration)

                // 验证提取的音频块时长
                val extractedDuration = getAudioDuration(chunkFile.absolutePath)
                logger.writeLog("Extracted chunk duration: ${extractedDuration}s")

                // 处理音频块
                val chunkResult = processAudioChunkPrecisely(chunkFile.absolutePath, chunkDuration)
                
                // 保存处理结果到临时文件
                val vocalsChunkFile = File(tempDir, "chunk_${i}_vocals.wav")
                val accompanimentChunkFile = File(tempDir, "chunk_${i}_accompaniment.wav")
                
                saveAudioChunkWithPrecision(chunkResult.vocals, vocalsChunkFile.absolutePath, chunkDuration)
                saveAudioChunkWithPrecision(chunkResult.accompaniment, accompanimentChunkFile.absolutePath, chunkDuration)

                // 验证保存的音频块时长
                val vocalsChunkDuration = getAudioDuration(vocalsChunkFile.absolutePath)
                val accompanimentChunkDuration = getAudioDuration(accompanimentChunkFile.absolutePath)
                logger.writeLog("Saved chunks duration - Vocals: ${vocalsChunkDuration}s, Accompaniment: ${accompanimentChunkDuration}s")

                // 添加到文件列表
                vocalsChunkFiles.add(vocalsChunkFile.absolutePath)
                accompanimentChunkFiles.add(accompanimentChunkFile.absolutePath)
                
                // 清理临时文件
                chunkFile.delete()
                
                // 记录进度（以 0.0 - 1.0 的小数形式回调）
                val progressFraction = (i + 1).toDouble() / chunkCount.toDouble()
                logger.writeLog("Progress: ${String.format("%.3f", progressFraction)} ($i/$chunkCount chunks processed)")
                try {
                    progressCallback(progressFraction, "Processed chunk ${i + 1} of $chunkCount")
                } catch (e: Exception) {
                    logger.writeErrorLog("Error calling progressCallback: ${e.message}", e)
                }
            }
            
            // 所有块处理完成后，使用改进的合并方法
            logger.writeLog("All chunks processed, starting final merge...")
            try {
                progressCallback(0.95, "Merging chunks")
            } catch (e: Exception) {
                logger.writeErrorLog("Error calling progressCallback before merge: ${e.message}", e)
            }
            
            // 使用精确的合并方法
            mergeAudioFilesPrecisely(vocalsChunkFiles, vocalsOutputFile.absolutePath, durationSeconds)
            mergeAudioFilesPrecisely(accompanimentChunkFiles, accompanimentOutputFile.absolutePath, durationSeconds)
            try {
                progressCallback(1.0, "Separation complete")
            } catch (e: Exception) {
                logger.writeErrorLog("Error calling progressCallback after merge: ${e.message}", e)
            }
            
        } finally {
            // 清理所有临时文件
            try {
                tempDir.listFiles()?.forEach { file ->
                    if (file.name.startsWith("chunk_") && file.name.endsWith(".wav")) {
                        file.delete()
                    }
                }
            } catch (e: Exception) {
                logger.writeErrorLog("Error cleaning up temporary chunk files: ${e.message}", e)
            }
        }
    }

    /**
     * 精确提取音频片段 - 关键修复
     */
    private fun extractAudioChunkWithPrecision(inputFilePath: String, outputFilePath: String, startTime: Int, duration: Int) {
        val startTimeDouble = startTime.toDouble()
        
        // 使用更精确的提取参数
        // 关键改进：使用准确的seek和duration控制，添加处理卡顿的参数
        // 使用精确的时间控制，避免负时间戳
        // 使用 -ss 作为输入选项以提高准确性，并添加更多时间戳处理参数
        val cmd = "-ss $startTimeDouble -i \"$inputFilePath\" -t $duration -acodec pcm_s16le -ar $SAMPLE_RATE -ac $CHANNELS -y -avoid_negative_ts make_zero -fflags +genpts+igndts -async 1 \"$outputFilePath\""
        
        logger.writeLog("Extract command: $cmd")
        val session = FFmpegKit.execute(cmd)
        
        if (!ReturnCode.isSuccess(session.returnCode)) {
            val failStackTrace = session.getFailStackTrace() ?: "Unknown error"
            logger.writeErrorLog("FFmpeg extraction failed. Command: $cmd. Stack trace: $failStackTrace")
            throw RuntimeException("FFmpeg extraction failed. Stack trace: $failStackTrace")
        }
        
        logger.writeLog("Audio chunk extracted precisely to: $outputFilePath")
    }

    /**
     * 精确保存音频数据块
     */
    private fun saveAudioChunkWithPrecision(audioData: FloatArray, outputFilePath: String, duration: Int) {
        logger.writeLog("Saving audio chunk precisely to: $outputFilePath, data size: ${audioData.size}, duration: ${duration}s")
        
        // 检查音频数据
        if (audioData.isEmpty()) {
            logger.writeLog("Warning: audioData is empty, creating silent audio")
            val silentData = FloatArray(SAMPLE_RATE * 2 * duration) { 0f }
            saveAudioChunkWithPrecision(silentData, outputFilePath, duration)
            return
        }
        
        // 将浮点数组转换为WAV文件
        val tempRawFile = File(outputFilePath + "_raw.pcm")
        try {
            val rawBytes = ByteArray(audioData.size * 4)
            val byteBuffer = ByteBuffer.wrap(rawBytes).order(ByteOrder.LITTLE_ENDIAN)
            val floatBuffer = byteBuffer.asFloatBuffer()
            floatBuffer.put(audioData)
            
            tempRawFile.writeBytes(rawBytes)
            
            // 使用重编码保存为一致的 PCM（不在每段单独做 afade，合并时使用 acrossfade）
            val cmd = "-f f32le -ar $SAMPLE_RATE -ac $CHANNELS -i \"${tempRawFile.absolutePath}\" -acodec pcm_s16le -ar $SAMPLE_RATE -ac $CHANNELS -y -fflags +genpts+igndts -avoid_negative_ts make_zero -async 1 \"$outputFilePath\""

            logger.writeLog("Save command: $cmd")
            val session = FFmpegKit.execute(cmd)
            
            if (!ReturnCode.isSuccess(session.returnCode)) {
                val failStackTrace = session.getFailStackTrace() ?: "Unknown error"
                logger.writeErrorLog("Failed to save audio chunk precisely. Command: $cmd. Stack trace: $failStackTrace")
                throw RuntimeException("Failed to save audio chunk precisely. Stack trace: $failStackTrace")
            }
        } finally {
            tempRawFile.delete()
        }
        
        logger.writeLog("Audio chunk saved precisely to: $outputFilePath")
    }

    /**
     * 精确合并音频文件
     */
    private fun mergeAudioFilesPrecisely(inputFiles: List<String>, outputFilePath: String, totalDuration: Int) {
        logger.writeLog("Merging ${inputFiles.size} audio files precisely into: $outputFilePath")
        
        if (inputFiles.isEmpty()) {
            logger.writeLog("No input files to merge")
            return
        }
        
        if (inputFiles.size == 1) {
            // 如果只有一个文件，重编码复制以确保格式一致
            val cmd = "-i \"${inputFiles[0]}\" -acodec pcm_s16le -ar $SAMPLE_RATE -ac $CHANNELS -y \"$outputFilePath\""
            val session = FFmpegKit.execute(cmd)
            if (!ReturnCode.isSuccess(session.returnCode)) {
                val failStackTrace = session.getFailStackTrace() ?: "Unknown error"
                logger.writeErrorLog("Failed to copy single file. Stack trace: $failStackTrace")
                throw RuntimeException("Failed to copy single file. Stack trace: $failStackTrace")
            }
            return
        }
        
        // 创建文件列表
        val fileList = File(context.cacheDir, "filelist_${System.currentTimeMillis()}.txt")
        try {
            // 写入文件列表
            val fileListContent = StringBuilder()
            for (filePath in inputFiles) {
                val escapedPath = filePath.replace("'", "'\\\\''")
                fileListContent.append("file '$escapedPath'\n")
            }
            fileList.writeText(fileListContent.toString())
            
            // 如果启用了重叠（CHUNK_OVERLAP > 0），不要使用简单的 concat（会把重叠部分重复拼接），
            // 而是使用交叉淡化方法来平滑过渡并保持原始时长
            if (CHUNK_OVERLAP > 0) {
                try {
                    // 记录每段时长用于调试
                    var sumDur = 0.0
                    inputFiles.forEach { f ->
                        val d = getAudioDuration(f)
                        logger.writeLog("Input chunk duration: $f -> ${d}s")
                        sumDur += d
                    }
                    val expectedAfterCrossfade = sumDur - CHUNK_OVERLAP.toDouble() * (inputFiles.size - 1)
                    logger.writeLog("Sum of chunks: ${String.format("%.3f", sumDur)}s, expected after crossfade: ${String.format("%.3f", expectedAfterCrossfade)}s, original totalDuration: ${totalDuration}s")

                    // 直接使用交叉淡化合并
                    mergeWithAlternativeMethod(inputFiles, outputFilePath, totalDuration)
                    // 早返回，避免再走 concat
                    return
                } catch (e: Exception) {
                    logger.writeErrorLog("Crossfade merge failed, falling back to concat: ${e.message}", e)
                    // 如果交叉淡化出错，则继续尝试 concat
                }
            }

            // 使用精确的合并参数（无重叠或交叉淡化失败时备用）
            val concatCmd = "-f concat -safe 0 -i \"${fileList.absolutePath}\" -c:a pcm_s16le -ar $SAMPLE_RATE -ac $CHANNELS -avoid_negative_ts make_zero -fflags +genpts+igndts -async 1 -y \"$outputFilePath\""

            logger.writeLog("Precise merge command (fallback concat): $concatCmd")
            val session = FFmpegKit.execute(concatCmd)

            if (!ReturnCode.isSuccess(session.returnCode)) {
                val failStackTrace = session.getFailStackTrace() ?: "Unknown error"
                logger.writeErrorLog("Failed to merge audio files precisely. Stack trace: $failStackTrace")

                // 备用方案：使用不同的合并方法
                logger.writeLog("Trying alternative merge method...")
                mergeWithAlternativeMethod(inputFiles, outputFilePath, totalDuration)
            }
            
        } catch (e: Exception) {
            logger.writeErrorLog("Error merging audio files precisely: ${e.message}", e)
            
            // 备用方案
            logger.writeLog("Trying alternative merge method due to exception...")
            mergeWithAlternativeMethod(inputFiles, outputFilePath, totalDuration)
        } finally {
            // 清理文件列表
            try {
                fileList.delete()
            } catch (e: Exception) {
                logger.writeErrorLog("Error cleaning up file list: ${e.message}", e)
            }
        }
        
        logger.writeLog("Audio files merged precisely: $outputFilePath")
    }

    /**
     * 备用合并方法 - 使用交叉淡化处理重叠部分
     */
    private fun mergeWithAlternativeMethod(inputFiles: List<String>, outputFilePath: String, totalDuration: Int) {
        logger.writeLog("Using alternative merge method with crossfading")
        // 使用 ffmpeg filter_complex 实现链式 acrossfade，并映射最终输出流
        if (inputFiles.isEmpty()) return

        // 如果只有一个文件，直接重编码拷贝（防止复杂命令在短文件上出问题）
        if (inputFiles.size == 1) {
            val cmdSingle = "-i \"${inputFiles[0]}\" -acodec pcm_s16le -ar $SAMPLE_RATE -ac $CHANNELS -y \"$outputFilePath\""
            logger.writeLog("Single-file crossfade fallback command: $cmdSingle")
            val sessionSingle = FFmpegKit.execute(cmdSingle)
            if (!ReturnCode.isSuccess(sessionSingle.returnCode)) {
                val failStackTrace = sessionSingle.getFailStackTrace() ?: "Unknown error"
                logger.writeErrorLog("Single-file copy failed. Stack trace: $failStackTrace")
                throw RuntimeException("Single-file copy failed. Stack trace: $failStackTrace")
            }
            return
        }

        val buildResult = buildCrossfadeFilter(inputFiles.size)
        if (buildResult.isEmpty()) {
            throw RuntimeException("Unable to build crossfade filter")
        }

        val parts = buildResult.split('|')
        val filterComplex = parts[0]
        val finalLabel = if (parts.size > 1) parts[1] else "a01"

        val inputOptions = inputFiles.joinToString(" ") { file -> "-i \"$file\"" }
        val cmd = "$inputOptions -filter_complex \"$filterComplex\" -map \"[$finalLabel]\" -c:a pcm_s16le -ar $SAMPLE_RATE -ac $CHANNELS -avoid_negative_ts make_zero -fflags +genpts+igndts -async 1 -y \"$outputFilePath\""

        logger.writeLog("Crossfade merge command: $cmd")
        val session = FFmpegKit.execute(cmd)
        if (!ReturnCode.isSuccess(session.returnCode)) {
            val failStackTrace = session.getFailStackTrace() ?: "Unknown error"
            logger.writeErrorLog("Crossfade merge failed. Stack trace: $failStackTrace")
            throw RuntimeException("Crossfade merge failed. Stack trace: $failStackTrace")
        }
    }
    
    /**
     * 构建交叉淡化滤镜
     */
    private fun buildCrossfadeFilter(fileCount: Int): String {
        if (fileCount <= 1) return ""
        // 使用重叠秒数作为淡化时长（至少 0.01s）
        val overlap = max(0.01f, CHUNK_OVERLAP.toFloat())

        // 构建链式 acrossfade，例如：
        // [0:a][1:a]acrossfade=d=overlap:c1=tri:c2=tri[a01];[a01][2:a]acrossfade=d=overlap:c1=tri:c2=tri[a02];...
        val sb = StringBuilder()

        // 第一次跨淡
        sb.append("[0:a][1:a]acrossfade=d=$overlap:c1=tri:c2=tri[a01];")

        // 依次链式跨淡
        for (i in 2 until fileCount) {
            val prevLabel = if (i == 2) "a01" else "a${String.format("%02d", i - 1)}"
            val curLabel = "a${String.format("%02d", i)}"
            sb.append("[${prevLabel}][${i}:a]acrossfade=d=$overlap:c1=tri:c2=tri[${curLabel}];")
        }

        // 移除末尾分号
        if (sb.isNotEmpty()) sb.setLength(sb.length - 1)

        // 最终输出标签
        val finalLabel = if (fileCount == 2) "a01" else "a${String.format("%02d", fileCount - 1)}"

        // 返回滤镜字符串并附加最终标签，调用方会解析
        return sb.toString() + "|" + finalLabel
    }

    /**
     * 精确处理音频片段
     */
    private fun processAudioChunkPrecisely(chunkFilePath: String, duration: Int): SeparationResult {
        logger.writeLog("Processing audio chunk precisely: $chunkFilePath, duration: ${duration}s")
        
        // 加载音频数据
        val audioData = loadAudioDataPrecisely(chunkFilePath)
        logger.writeLog("Audio data loaded precisely, size: ${audioData.size}")
        
        if (audioData.isEmpty()) {
            logger.writeLog("Audio data is empty, returning default result")
            return SeparationResult(FloatArray(0), FloatArray(0))
        }

        val totalFrames = audioData.size / 2
        val expectedFrames = duration * SAMPLE_RATE
        
        logger.writeLog("Total frames: $totalFrames, Expected frames: $expectedFrames")
        
        // 确保处理的数据符合预期的持续时间
        return processAudioChunkWithExactDuration(audioData, duration)
    }

    /**
     * 精确加载音频数据
     */
    private fun loadAudioDataPrecisely(filePath: String): FloatArray {
        val tempRawFile = File(filePath + "_raw.pcm")
        // 添加更多时间戳处理参数确保正确加载
        val cmd = "-i \"$filePath\" -f f32le -ar $SAMPLE_RATE -ac $CHANNELS -fflags +genpts+igndts -avoid_negative_ts make_zero -y \"${tempRawFile.absolutePath}\""
        
        val session = FFmpegKit.execute(cmd)
        
        if (!ReturnCode.isSuccess(session.returnCode)) {
            val failStackTrace = session.getFailStackTrace() ?: "Unknown error"
            logger.writeErrorLog("FFmpeg execution failed. Command: $cmd. Stack trace: $failStackTrace")
            if (tempRawFile.exists()) tempRawFile.delete()
            throw RuntimeException("FFmpeg execution failed. Stack trace: $failStackTrace")
        }
        
        if (!tempRawFile.exists()) {
            logger.writeErrorLog("Converted PCM file does not exist")
            throw RuntimeException("Converted PCM file does not exist")
        }
        
        // 读取原始 PCM 数据
        val rawData = tempRawFile.readBytes()
        tempRawFile.delete()
        
        if (rawData.isEmpty()) {
            logger.writeLog("Raw PCM data is empty")
            return FloatArray(0)
        }
        
        // 将字节数据转换为浮点数组
        val floatArray = FloatArray(rawData.size / 4)
        val byteBuffer = ByteBuffer.wrap(rawData).order(ByteOrder.LITTLE_ENDIAN)
        val floatBuffer = byteBuffer.asFloatBuffer()
        floatBuffer.get(floatArray)
        
        logger.writeLog("Loaded audio data precisely with ${floatArray.size} samples")
        return floatArray
    }

    /**
     * 计算精确的音频块
     */
    private fun calculatePreciseChunks(totalSeconds: Int): List<Pair<Int, Int>> {
        val chunks = mutableListOf<Pair<Int, Int>>()
        
        if (totalSeconds <= CHUNK_DURATION) {
            chunks.add(Pair(0, totalSeconds))
            return chunks
        }

        // 使用重叠：每个片段长度为 CHUNK_DURATION，片段之间重叠 CHUNK_OVERLAP 秒
        val step = max(1, CHUNK_DURATION - CHUNK_OVERLAP)
        var startTime = 0
        while (startTime < totalSeconds) {
            val remainingSeconds = totalSeconds - startTime
            val chunkDuration = if (remainingSeconds <= CHUNK_DURATION) remainingSeconds else CHUNK_DURATION
            chunks.add(Pair(startTime, chunkDuration))
            startTime += step
        }
        
        return chunks
    }

    // 以下方法保持不变
    private fun processAudioChunkWithExactDuration(audioData: FloatArray, duration: Int): SeparationResult {
        logger.writeLog("Processing audio chunk with exact duration: ${duration}s")
        
        val expectedFrames = duration * SAMPLE_RATE
        val expectedSamples = expectedFrames * 2
        
        // 确保音频数据大小符合预期
        val adjustedAudioData = if (audioData.size > expectedSamples) {
            audioData.copyOf(expectedSamples)
        } else if (audioData.size < expectedSamples) {
            FloatArray(expectedSamples).also { paddedData ->
                System.arraycopy(audioData, 0, paddedData, 0, audioData.size)
            }
        } else {
            audioData
        }
        
        try {
            // 处理当前段
            val segmentResult = processSingleSegment(adjustedAudioData, expectedFrames)
            
            // 确保输出也符合预期大小
            val vocalsOutput = if (segmentResult.vocals.size > expectedSamples) {
                segmentResult.vocals.copyOf(expectedSamples)
            } else if (segmentResult.vocals.size < expectedSamples) {
                FloatArray(expectedSamples).also { paddedData ->
                    System.arraycopy(segmentResult.vocals, 0, paddedData, 0, segmentResult.vocals.size)
                }
            } else {
                segmentResult.vocals
            }
            
            val accompanimentOutput = if (segmentResult.accompaniment.size > expectedSamples) {
                segmentResult.accompaniment.copyOf(expectedSamples)
            } else if (segmentResult.accompaniment.size < expectedSamples) {
                FloatArray(expectedSamples).also { paddedData ->
                    System.arraycopy(segmentResult.accompaniment, 0, paddedData, 0, segmentResult.accompaniment.size)
                }
            } else {
                segmentResult.accompaniment
            }
            
            logger.writeLog("Segment processing completed successfully")
            
            return SeparationResult(vocalsOutput, accompanimentOutput)
            
        } catch (e: Exception) {
            logger.writeErrorLog("Error in audio chunk processing: ${e.message}", e)
            // 返回原始音频作为后备
            return SeparationResult(adjustedAudioData.copyOf(), adjustedAudioData.copyOf())
        }
    }

    private fun processSingleSegment(audioData: FloatArray, segmentFrames: Int): SeparationResult {
        logger.writeLog("Processing single segment with $segmentFrames frames")
        
        try {
            // 准备输入
            val inputBuffer = prepareInput(audioData, segmentFrames)
            
            // 执行推理
            val outputBuffers = runInference(inputBuffer, segmentFrames)
            
            // 解析输出
            val vocalsOutput = parseOutput(outputBuffers[1], segmentFrames)
            val accompanimentOutput = parseOutput(outputBuffers[0], segmentFrames)
            
            logger.writeLog("Segment processing completed successfully")
            
            return SeparationResult(vocalsOutput, accompanimentOutput)
            
        } catch (e: Exception) {
            logger.writeErrorLog("Error in single segment processing: ${e.message}", e)
            // 返回原始音频作为后备
            return SeparationResult(audioData.copyOf(), audioData.copyOf())
        }
    }

    private fun prepareInput(audioData: FloatArray, inputFrames: Int): ByteBuffer {
        val inputSize = inputFrames * 2 * 4
        val inputBuffer = ByteBuffer.allocateDirect(inputSize)
        inputBuffer.order(ByteOrder.nativeOrder())
        
        val floatBuffer = inputBuffer.asFloatBuffer()
        
        // 确保数据大小精确匹配
        val expectedSize = inputFrames * 2
        if (audioData.size >= expectedSize) {
            floatBuffer.put(audioData, 0, expectedSize)
        } else {
            // 用0填充不足的数据
            floatBuffer.put(audioData)
            val remaining = expectedSize - audioData.size
            for (i in 0 until remaining) {
                floatBuffer.put(0f)
            }
        }
        
        inputBuffer.rewind()
        return inputBuffer
    }

    private fun runInference(inputBuffer: ByteBuffer, inputFrames: Int): Array<ByteBuffer> {
        if (interpreter == null) {
            throw IllegalStateException("2stems model not loaded")
        }
        
        try {
            // 调整输入形状
            interpreter!!.resizeInput(0, intArrayOf(inputFrames, 2))
            interpreter!!.allocateTensors()
            
            // 准备输出缓冲区
            val outputBuffer0 = ByteBuffer.allocateDirect(inputFrames * 2 * 4)
            outputBuffer0.order(ByteOrder.nativeOrder())
            
            val outputBuffer1 = ByteBuffer.allocateDirect(inputFrames * 2 * 4)
            outputBuffer1.order(ByteOrder.nativeOrder())
            
            // 执行推理
            val outputMap = HashMap<Int, Any>()
            outputMap[0] = outputBuffer0
            outputMap[1] = outputBuffer1
            
            interpreter!!.runForMultipleInputsOutputs(arrayOf(inputBuffer), outputMap)
            
            outputBuffer0.rewind()
            outputBuffer1.rewind()
            
            return arrayOf(outputBuffer0, outputBuffer1)
        } catch (e: Exception) {
            logger.writeErrorLog("Error during inference: ${e.message}", e)
            throw e
        }
    }

    private fun parseOutput(outputBuffer: ByteBuffer, expectedFrames: Int): FloatArray {
        outputBuffer.rewind()
        val floatCount = outputBuffer.capacity() / 4
        val expectedSize = expectedFrames * 2
        
        val result = FloatArray(expectedSize)
        
        if (floatCount >= expectedSize) {
            outputBuffer.asFloatBuffer().get(result, 0, expectedSize)
        } else {
            if (floatCount > 0) {
                outputBuffer.asFloatBuffer().get(result, 0, min(floatCount, expectedSize))
            }
            // 用0填充不足的部分
            for (i in floatCount until expectedSize) {
                result[i] = 0f
            }
            logger.writeLog("Output size insufficient, padded with zeros")
        }
        
        return result
    }

    private fun getOutputDirectory(songName: String): File {
        val cleanSongName = cleanFileName(songName)
        val downloadsDir = Environment.getExternalStoragePublicDirectory(Environment.DIRECTORY_DOWNLOADS)
        val outputDir = File(downloadsDir, "yugeApp/Music/$cleanSongName")
        
        if (!outputDir.exists()) {
            outputDir.mkdirs()
        }
        
        return outputDir
    }
    
    private fun cleanFileName(fileName: String): String {
        return fileName.replace(Regex("[<>:\"/\\\\|?*]"), "_")
    }

    private fun getAudioDuration(filePath: String): Double {
        val retriever = MediaMetadataRetriever()
        try {
            retriever.setDataSource(filePath)
            val durationStr = retriever.extractMetadata(MediaMetadataRetriever.METADATA_KEY_DURATION)
            return durationStr?.toDoubleOrNull()?.div(1000) ?: 0.0
        } catch (e: Exception) {
            logger.writeErrorLog("Error getting audio duration: ${e.message}", e)
            return 0.0
        } finally {
            retriever.release()
        }
    }

    fun release() {
        interpreter?.close()
        interpreter = null
        logger.writeLog("Model released")
    }

    data class SeparationResult(
        val vocals: FloatArray,
        val accompaniment: FloatArray
    )
}
```

新建 `AudioSeparatorManager.kt` 文件
```
// AudioSeparatorManager.kt
package com.yugeaudiovisual

import com.facebook.react.bridge.Arguments
import com.facebook.react.bridge.Promise
import com.facebook.react.bridge.ReactApplicationContext
import com.facebook.react.bridge.ReactContextBaseJavaModule
import com.facebook.react.bridge.ReactMethod
import com.facebook.react.bridge.WritableMap
import com.facebook.react.modules.core.DeviceEventManagerModule
import android.util.Log
import java.io.File

class AudioSeparatorManager(reactContext: ReactApplicationContext) : ReactContextBaseJavaModule(reactContext) {
    companion object {
        private const val TAG = "AudioSeparatorManager"
    }

    private var audioSeparator: AudioSeparator? = null
    private val logger = Logger(TAG)

    override fun getName(): String {
        return "AudioSeparatorManager"
    }

    @ReactMethod
    fun initialize(promise: Promise) {
        try {
            logger.writeLog("Initializing AudioSeparator")
            Log.d(TAG, "Initializing AudioSeparator")
            
            if (audioSeparator == null) {
                audioSeparator = AudioSeparator(reactApplicationContext)
                logger.writeLog("AudioSeparator initialized")
                Log.d(TAG, "AudioSeparator initialized")
                promise.resolve("AudioSeparator initialized successfully")
            } else {
                logger.writeLog("AudioSeparator already initialized")
                Log.d(TAG, "AudioSeparator already initialized")
                promise.resolve("AudioSeparator already initialized")
            }
        } catch (e: Exception) {
            logger.writeErrorLog("Error initializing AudioSeparator: ${e.message}", e)
            Log.e(TAG, "Error initializing AudioSeparator: ${e.message}")
            promise.reject("INIT_ERROR", "Failed to initialize AudioSeparator", e)
        }
    }

    @ReactMethod
    fun separateAudio(inputFilePath: String, originalFileName: String, promise: Promise) {
        try {
            logger.writeLog("Received request to separate audio: $inputFilePath")
            Log.d(TAG, "Received request to separate audio: $inputFilePath")
            
            if (audioSeparator == null) {
                val errorMsg = "AudioSeparator not initialized"
                logger.writeErrorLog(errorMsg)
                Log.e(TAG, errorMsg)
                promise.reject("NOT_INITIALIZED", errorMsg)
                return
            }

            logger.writeLog("Starting audio separation for file: $inputFilePath")
            Log.d(TAG, "Starting audio separation for file: $inputFilePath")
            
            // 将进度事件通过 DeviceEventEmitter 发送给 JS
            val emitter = reactApplicationContext.getJSModule(DeviceEventManagerModule.RCTDeviceEventEmitter::class.java)

            audioSeparator?.separateAudio(inputFilePath, originalFileName,
                { progressFraction: Double, message: String ->
                    try {
                        val map: WritableMap = Arguments.createMap()
                        map.putDouble("progress", progressFraction)
                        map.putString("message", message)
                        emitter.emit("AudioSeparationProgress", map)
                    } catch (e: Exception) {
                        logger.writeErrorLog("Error emitting progress event: ${e.message}", e)
                    }
                },
                { result ->
                try {
                    result.fold(
                        onSuccess = { map ->
                            logger.writeLog("Audio separation completed successfully")
                            Log.d(TAG, "Audio separation completed successfully")
                            val resultMap: WritableMap = Arguments.createMap()
                            resultMap.putString("status", "success")
                            // map expected to contain vocals & accompaniment
                            resultMap.putString("vocals", map["vocals"])
                            resultMap.putString("accompaniment", map["accompaniment"])
                            promise.resolve(resultMap)
                        },
                        onFailure = { exception ->
                            logger.writeErrorLog("Audio separation failed: ${exception.message}", exception)
                            Log.e(TAG, "Audio separation failed: ${exception.message}")
                            promise.reject("SEPARATION_ERROR", "Audio separation failed", exception)
                        }
                    )
                } catch (e: Exception) {
                    logger.writeErrorLog("Error handling separation result: ${e.message}", e)
                    Log.e(TAG, "Error handling separation result: ${e.message}")
                    promise.reject("RESULT_ERROR", "Error handling separation result", e)
                }
            })
        } catch (e: Exception) {
            logger.writeErrorLog("Error starting audio separation: ${e.message}", e)
            Log.e(TAG, "Error starting audio separation: ${e.message}")
            promise.reject("SEPARATION_START_ERROR", "Error starting audio separation", e)
        }
    }

    @ReactMethod
    fun release(promise: Promise) {
        try {
            logger.writeLog("Releasing AudioSeparator")
            Log.d(TAG, "Releasing AudioSeparator")
            
            audioSeparator?.release()
            audioSeparator = null
            logger.writeLog("AudioSeparator released")
            Log.d(TAG, "AudioSeparator released")
            promise.resolve("AudioSeparator released successfully")
        } catch (e: Exception) {
            logger.writeErrorLog("Error releasing AudioSeparator: ${e.message}", e)
            Log.e(TAG, "Error releasing AudioSeparator: ${e.message}")
            promise.reject("RELEASE_ERROR", "Failed to release AudioSeparator", e)
        }
    }

    override fun invalidate() {
        super.invalidate()
        try {
            logger.writeLog("AudioSeparatorManager invalidate called")
            Log.d(TAG, "AudioSeparatorManager invalidate called")
            
            audioSeparator?.release()
            audioSeparator = null
            logger.writeLog("AudioSeparator released in invalidate")
            Log.d(TAG, "AudioSeparator released in invalidate")
        } catch (e: Exception) {
            logger.writeErrorLog("Error releasing AudioSeparator in invalidate: ${e.message}", e)
            Log.e(TAG, "Error releasing AudioSeparator in invalidate: ${e.message}")
        }
    }
}
```
新建 `AudioSeparationPackage.kt` 文件
```
// AudioSeparationPackage.kt 与react native交互
package com.yugeaudiovisual

import com.facebook.react.ReactPackage
import com.facebook.react.bridge.NativeModule
import com.facebook.react.bridge.ReactApplicationContext
import com.facebook.react.uimanager.ViewManager

class AudioSeparationPackage : ReactPackage {
    override fun createNativeModules(reactContext: ReactApplicationContext): List<NativeModule> {
        return listOf(AudioSeparatorManager(reactContext))
    }

    override fun createViewManagers(reactContext: ReactApplicationContext): List<ViewManager<*, *>> {
        return emptyList()
    }
}
```