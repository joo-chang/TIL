## Swagger

Swagger는 OAS를 위한 프레임워크이며 API를 갖고 있는 specification을 정의할 수 있는 툴 중 하나이다. API 문서 자동화, 파라미터를 넣고 테스트를 할 수 있다.

API 문서 작성 시간을 단축시킬 수 있고, API 정보를 실시간으로 유지할 수 있다.

> OSA(OpenAPI Specification)
OSA는 HTTP API의 인터페이스를 정의하는 문서이다.

<br>

이제부터 기본적인 Swagger 사용 방법을 알아보자.

### 의존성 추가

```java
implementation 'io.springfox:springfox-boot-starter:3.0.0'
```

### SwaggerConfig

```java
@Configuration
public class SwaggerConfig {
    private static final String API_NAME = "EveryLetter API";
    private static final String API_VERSION = "0.0.1";
    private static final String API_DESCRIPTION = "EveryLetter API 명세서 입니다.";

    @Bean
    public Docket api() {
        return new Docket(DocumentationType.OAS_30)
                .select()
                .apis(RequestHandlerSelectors.any())
                .paths(PathSelectors.any())
                .build()
                .securityContexts(List.of(this.securityContext())) // SecurityContext 설정
                .securitySchemes(List.of(this.apiKey())) // ApiKey 설정
                .apiInfo(apiInfo());
    }
    // JWT SecurityContext 구성
    private SecurityContext securityContext() {
        return SecurityContext.builder()
                .securityReferences(defaultAuth())
                .build();
    }

    private List<SecurityReference> defaultAuth() {
        AuthorizationScope authorizationScope = new AuthorizationScope("global", "accessEverything");
        AuthorizationScope[] authorizationScopes = new AuthorizationScope[1];
        authorizationScopes[0] = authorizationScope;
        return List.of(new SecurityReference("Authorization", authorizationScopes));
    }

    // ApiKey 정의
    private ApiKey apiKey() {
        return new ApiKey("Authorization", "Authorization", "header");
    }
    private ApiInfo apiInfo() {
        return new ApiInfoBuilder()
                .title(API_NAME)
                .description(API_DESCRIPTION)
                .version(API_VERSION)
                .build();
    }
}
```

Config에 기본적인 내용을 세팅한다. 
필자는 JWT를 사용하기 때문에 `.securityContexts()` 와 `.securitySchemes()` 를 추가 설정하였다.

![](https://velog.velcdn.com/images/joo-chang/post/54fc31a1-477c-427c-a0f8-7529226ac9a6/image.png)
![](https://velog.velcdn.com/images/joo-chang/post/a16c42a3-3527-41e7-8d57-cfdf4be612e9/image.png)

`http://localhost:8080/swagger-ui/index.html`

서버 실행 후 위 URL에 접근 해보면, 초기 화면이 설정되고 Authorize 값을 세팅할 수 있도록 추가된다.
<br>

### Controller

```java
@Api(tags = "커뮤니티")
@RestController
@RequestMapping("/post")
@RequiredArgsConstructor
public class PostController {

    private final PostService postService;

    @Operation(summary = "게시글 작성", description = "게시글 작성 API 입니다. \n Category, User ID가 필요합니다.")
    @ApiResponses({
            @ApiResponse(responseCode = "200", description = "정상"),
            @ApiResponse(responseCode = "500", description = "서버 에러")
    })
    @PostMapping("/write")
    public ApiSuccResp<PostWriteResp> postWrite(@RequestBody PostWriteReq postWriteReq) {

        PostWriteResp postWriteResp = postService.postWrite(postWriteReq);

        return ApiSuccResp.from(postWriteResp);
    }
}
```

### DTO

```java
@Getter
@NoArgsConstructor
@AllArgsConstructor
public class PostWriteReq {
    @ApiModelProperty(value = "제목", example = "제목01")
    private String title;
    @ApiModelProperty(value = "내용", example = "내용01")
    private String content;
    @ApiModelProperty(value = "카테고리 ID", example = "1")
    private Long categoryId;
    @ApiModelProperty(value = "사용자 ID", example = "1", hidden = true)
    private Long userId;
}
```

- @Api: Swagger에 나타낼 Controller의 이름 지정
- @Operation: API에 대한 요약 정보(summary)와 설명(decription) 작성
- @ApiResponse: API의 HTTP Status Code 반환 값에 대한 설명을 작성
    - @ApiResponses 어노테이션을 이용해 여러 개의 반환 값을 작성할 수 있다.
- @Parameter: 파라미터에 대한 설명(decription)와 예시(example)을 작성
- @ApiModelProperty: DTO 필드에 대한 설명(decription)와 예시(example)을 작성 

필요하지 않은 값을 숨길(hidden) 수도 있다.

![](https://velog.velcdn.com/images/joo-chang/post/baaa0ab6-771a-4849-99d1-125dcd0a6a2f/image.png)

<br>

## 결론

이렇게 Spring에서 Swagger를 사용하는 방법을 간단하게 알아보았다.
세부 내용들이 많지만 기본적으로 어떻게 동작되는지 정도만 적용해 보았다.