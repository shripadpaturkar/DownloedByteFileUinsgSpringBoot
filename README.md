# DownloedByteFileUinsgSpringBoot


//Controller

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.core.io.ByteArrayResource;
import org.springframework.core.io.Resource;
import org.springframework.http.HttpHeaders;
import org.springframework.http.MediaType;
import org.springframework.http.ResponseEntity;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.PathVariable;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RestController;

@RestController
@RequestMapping("/files")
public class FileController {
    @Autowired
    private FileRepository fileRepository;

    @GetMapping("/{id}")
    public ResponseEntity<Resource> downloadFile(@PathVariable Long id) {
        // Retrieve the file entity from the database
        FileEntity fileEntity = fileRepository.findById(id)
                .orElseThrow(() -> new RuntimeException("File not found"));

        // Convert the byte array to a resource
        Resource resource = new ByteArrayResource(fileEntity.getData());

        return ResponseEntity.ok()
                .contentType(MediaType.APPLICATION_OCTET_STREAM)
                .header(HttpHeaders.CONTENT_DISPOSITION, "attachment; filename=\"" + fileEntity.getFilename() + "\"")
                .body(resource);
    }
}



//Entity

import javax.persistence.*;

@Entity
@Table(name = "files")
public class FileEntity {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    private String filename;

    @Lob
    private byte[] data;

    // getters and setters
}


//Repository

import org.springframework.data.jpa.repository.JpaRepository;

public interface FileRepository extends JpaRepository<FileEntity, Long> {
}



/////////////
import java.io.*;

public class FileToByteArrayExample {

    public static void main(String[] args) {
        String folderName = "folderName"; // Replace with your folder name
        
        try {
            // Step 1: Get the URL representing the folder in the classpath
            ClassLoader classLoader = FileToByteArrayExample.class.getClassLoader();
            URL folderUrl = classLoader.getResource(folderName);
            
            // Step 2: Convert the URL to a file path
            String folderPath = folderUrl.getPath();
            
            // Step 3: Create a File object using the obtained file path
            File folder = new File(folderPath);
            
            // Step 4: Iterate over the files in the folder
            File[] files = folder.listFiles();
            for (File file : files) {
                // Step 5: Read each file into a byte array
                byte[] byteArray = readFileToByteArray(file);
                
                // Use the byte array as needed
                // ...
            }
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
    
    private static byte[] readFileToByteArray(File file) throws IOException {
        FileInputStream fis = new FileInputStream(file);
        ByteArrayOutputStream bos = new ByteArrayOutputStream();
        
        byte[] buffer = new byte[1024];
        int bytesRead;
        while ((bytesRead = fis.read(buffer)) != -1) {
            bos.write(buffer, 0, bytesRead);
        }
        
        fis.close();
        bos.close();
        
        return bos.toByteArray();
    }
}

/////////////////////////
import java.io.FileWriter;
import java.io.IOException;
import java.util.HashMap;
import com.google.gson.Gson;

public class GetMetaInfo {
    private HashMap<String, String> yourHashMap; // Replace with your actual HashMap

    public void storeDataToJsonFile() {
        Gson gson = new Gson();
        String json = gson.toJson(yourHashMap);

        String fileName = "data.json";
        String filePath = System.getProperty("user.dir") + "/" + fileName;

        try (FileWriter fileWriter = new FileWriter(filePath)) {
            fileWriter.write(json);
            System.out.println("Data stored to JSON file: " + filePath);
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
}
