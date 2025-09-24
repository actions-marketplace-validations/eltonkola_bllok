package com.eltonkola

import com.eltonkola.engine.Log
import com.eltonkola.model.BllokConfig
import com.eltonkola.model.buildCategoryTree
import com.eltonkola.model.parseYamlConfig
import java.io.File
import java.time.Instant
import kotlin.system.exitProcess


fun main(args: Array<String>) {

    val startedAt = Instant.now()
    Log.println("Bllok start!")
    if(args.size >= 3) {
        val templatePath = args[0]
        val inputPath = args[1]
        val outputPath = args[2]

        val rootPath = if(args.size > 3) args[3] else null

        Log.println("templatePath: $templatePath", true)
        Log.println("inputPath: $inputPath", true)
        Log.println("outputPath: $outputPath", true)
        Log.println("rootPath: $rootPath", true)


        if(templatePath.isEmpty() || inputPath.isEmpty() || outputPath.isEmpty()) {
            Log.println("!!! please pass the template and output path sa parameters !!!", true)
            exitProcess(1)
        }
        Bllok(BllokConfig(
            templatePath = templatePath,
            inputPath = inputPath,
            outputPath = outputPath,
            rootPath = rootPath,
            debug = false
        )).execute()
    }else{
        Log.println("!!! please pass the template and output path sa parameters !!!", true)
        exitProcess(1)
    }
    Log.println("Bllok end!", true)
    Log.println("Executed in: ${Instant.now().toEpochMilli() - startedAt.toEpochMilli() } millisec!", true)
    exitProcess(0)
}
class Bllok(
    private val options: BllokConfig
){
    fun execute() {
        Log.showLogs = options.debug

        Log.println("templatePath: ${options.templatePath}", true)
        Log.println("inputPath: ${options.inputPath}", true)
        Log.println("outputPath: ${options.outputPath}", true)

        //blog settings
        val config = parseYamlConfig("${options.inputPath}/bllok.yaml")
        //tree of blog pages to create
        val contentTree = buildCategoryTree(directory = File(options.inputPath))

        if (options.debug) {
            Log.println("Blog title: ${config.websiteName}")
            contentTree.printTree()
        }

        //create root publish folder
        File(options.outputPath).clearFolder()
        copyStaticFiles(File(options.templatePath), File(options.outputPath))

        BllokGenerator(
            config = config,
            root = contentTree,
            isRoot = true,
            options = options
        ).generate()

    }

    fun File.clearFolder() {
        if (this.exists()) {
            this.listFiles()?.forEach { it.deleteRecursively() }
        } else {
            this.mkdirs()
        }
    }
    fun copyStaticFiles(from: File, to: File) {
        if (!from.exists() || !from.isDirectory) {
            error("Source folder '${from.path}' does not exist or is not a directory.")
        }
        if (!to.exists()) to.mkdirs()

        from.walkTopDown().forEach { file ->
            val relative = file.relativeTo(from)
            val target = File(to, relative.path)

            // Skip excluded files and folders
            if (
                file.name == "index.html" ||
                file.name == "post.html" ||
                file.name.startsWith("partial_")
            ) return@forEach

            if (file.isDirectory) {
                target.mkdirs()
            } else {
                file.copyTo(target, overwrite = true)
            }
        }
    }



}
