## Spring Boot S3 연동

### 의존성 추가

```
implementation 'org.springframework.cloud:spring-cloud-starter-aws:2.2.1.RELEASE'
```

### application.yml 추가

```yaml
spring:  
	servlet:  
        multipart:  
	        max-file-size: 50MB  
	        max-request-size: 50MB  
	  
cloud:  
	aws:  
        s3:  
	        bucket: mention-bucket.bucket  
	region:  
		static: ap-northeast-2  
        auto: false  
	stack:  
        auto: false  
	credentials:  
        access-key: {발급받은 access key}  
        secret-key: {발급받은 secret key}
```

### Application 에 static 변수 추가

### S3Config.java 추가

S3 클라이언트를 생성한다. 이 클라이언트를 사용하여 S3 버킷에 액세스할 수 있다.

```java
import com.amazonaws.auth.AWSStaticCredentialsProvider;
import com.amazonaws.auth.BasicAWSCredentials;
import com.amazonaws.services.s3.AmazonS3Client;
import com.amazonaws.services.s3.AmazonS3ClientBuilder;
import org.springframework.beans.factory.annotation.Value;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;

@Configuration
public class S3Config {
    @Value("${cloud.aws.credentials.access-key}")
    private String iamAccessKey;
    @Value("${cloud.aws.credentials.secret-key}")
    private String iamSecretKey;
    @Value("${cloud.aws.region.static}")
    private String region;
    @Bean
    public AmazonS3Client amazonS3Client(){
        BasicAWSCredentials awsCredentials = new BasicAWSCredentials(iamAccessKey, iamSecretKey);
        return (AmazonS3Client) AmazonS3ClientBuilder.standard()
                .withRegion(region).enablePathStyleAccess()
                .withCredentials(new AWSStaticCredentialsProvider(awsCredentials))
                .build();
    }
}
```



### 프론트 요청

```js
const blobData = /* Blob 데이터 */;
const formData = new FormData();
formData.append('file', blobData, 'filename.ext'); // 'file'은 백엔드에서 필드 이름, 'filename.ext'는 파일 이름

fetch('https://example.com/file/upload', {
  method: 'POST',
  body: formData
})
.then(response => {
  // 업로드 완료 후 백엔드의 응답 처리
})
.catch(error => {
  console.error('업로드 중 오류 발생:', error);
});

```

### Controller

```java
@RestController  
@RequestMapping("/file")  
@RequiredArgsConstructor  
public class FileController {  
  
    private final FileService fileService;  
  
   @PostMapping("/upload")  
   public ApiSuccResp<String> uploadFile(@RequestParam("file") MultipartFile file) {  
       return ApiSuccResp.from(fileService.fileUpload(file));  
   }  
}
```

### Service

```java
@Service  
@RequiredArgsConstructor  
public class FileService {  
  
    private final AmazonS3Client amazonS3Client;  
  
    @Value("${cloud.aws.s3.bucket}")  
    private String bucket;  
    @Value("${cloud.aws.region.static}")  
    private String region;  
    @Value("${cloud.aws.fileLocation}")  
    private String fileLocation;  
  
    public String fileUpload(MultipartFile file) {  
        try {  
            String fileName = UUID.randomUUID() + "_" + file.getOriginalFilename();  
            fileName = fileName.replaceAll(" ", "");  
            String fileUrl = String.format(fileLocation, bucket, region, fileName);  
            ObjectMetadata metadata = new ObjectMetadata();  
            metadata.setContentType(file.getContentType());  
            metadata.setContentLength(file.getSize());  
            // S3에 저장 요청
            amazonS3Client.putObject(bucket, fileName, file.getInputStream(), metadata);  
            return fileUrl;  
        } catch (IOException e) {  
            throw ServiceException.INTERNAL_SERVER_ERROR;  
        }  
    }  
}
```

![[Pasted image 20240111152530.png]]

```sql
-- auto-generated definition  
create table T_COMMON_FILE  
(  
    FILE_SEQ           bigint auto_increment comment '파일 일련번호'  
        primary key,  
    CONTENTS_CD        varchar(16)                          not null comment '컨텐츠 코드(공통코드)',  
    CONTENTS_SEQ       bigint                               not null comment '컨텐츠 일련번호',  
    FILE_PATH          varchar(256)                         not null comment '파일 경로',  
    FILE_NAME          varchar(128)                         not null comment '파일 명',  
    FILE_ENCODING_NAME varchar(128)                         not null comment '인코딩 파일 명',  
    FILE_EXTENSION     varchar(11)                          not null comment '확장자',  
    FILE_SIZE          varchar(16)                          not null comment '파일크기',  
    `ORDER`            int                                  not null comment '정렬순서',  
    DEL_YN             char                                 not null comment '삭제여부',  
    REG_USER_ID        varchar(64)                          not null comment '등록자 ID',  
    REG_DATE           datetime default current_timestamp() not null comment '등록일시',  
    MOD_USER_ID        varchar(64)                          not null comment '수정자 ID',  
    MOD_DATE           datetime default current_timestamp() not null on update current_timestamp() comment '수정일시'  
)  
    comment '공통 파일테이블' charset = utf8mb4;
```

