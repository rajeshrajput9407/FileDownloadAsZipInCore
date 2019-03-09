# File Download As Zip In Core
File Download As Zip In Core



           List<SourceFile> sourceFiles = new List<SourceFile>();
            string webRootPath = _hostingEnvironment.WebRootPath;
            string contentRootPath = _hostingEnvironment.ContentRootPath;
            var provider = new PhysicalFileProvider(contentRootPath);
            var contents = provider.GetDirectoryContents(string.Empty);
            //var fileInfo = provider.GetFileInfo("wwwroot/js/site.js");
            var fileInfo = provider.GetFileInfo("22.png");
            byte[] bytes = System.IO.File.ReadAllBytes(fileInfo.PhysicalPath);
            // the output bytes of the zip
            byte[] fileBytes = null;
            SourceFile sourceFile = new SourceFile() {
                Name = fileInfo.Name,
                Extension = Path.GetExtension(fileInfo.Name),
                FileBytes = bytes

            };
            sourceFiles.Add(sourceFile);
            sourceFiles.Add(sourceFile);
            sourceFiles.Add(sourceFile);
            //sourceFiles.Add(sourceFile);
            // create a working memory stream
            using (System.IO.MemoryStream memoryStream = new System.IO.MemoryStream())
            {
                 // create a zip
                using (System.IO.Compression.ZipArchive zip = new System.IO.Compression.ZipArchive(memoryStream,                         System.IO.Compression.ZipArchiveMode.Create, true))
                {
                    // interate through the source files
                    foreach (SourceFile f in sourceFiles)
                    {
                        // add the item name to the zip
                        //System.IO.Compression.ZipArchiveEntry zipItem = zip.CreateEntry(f.Name + "." + f.Extension);
                        System.IO.Compression.ZipArchiveEntry zipItem = zip.CreateEntry(f.Name);
                        // add the item bytes to the zip entry by opening the original file and copying the bytes 
                        using (System.IO.MemoryStream originalFileMemoryStream = new System.IO.MemoryStream(f.FileBytes))
                        {
                            using (System.IO.Stream entryStream = zipItem.Open())
                            {
                                originalFileMemoryStream.CopyTo(entryStream);
                            }
                        }
                    }
                }
                fileBytes = memoryStream.ToArray();
            }

            // download the constructed zip
           // Response.AddHeader("Content-Disposition", "attachment; filename=download.zip");
            return File(fileBytes, "application/zip", "Archieve.Zip");
            }
