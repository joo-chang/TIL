## OWASP (Open Web Application Security Project)

OWASP Top 10은 웹 애플리케이션 보안에 관련된 가장 일반적이고 심각한 취약점 10가지를 정리한 목록입니다. 
OWASP(Open Web Application Security Project)는 웹 애플리케이션 보안을 개선하기 위해 전 세계 보안 전문가들이 공동으로 작업하는 비영리 단체입니다. 이 목록은 주기적으로 업데이트되며, 웹 애플리케이션을 개발하고 운영하는 데 있어 보안 위협을 이해하고 방지하는 데 도움을 줍니다.

1. **Injection**: SQL, NoSQL, OS 명령어, LDAP 인젝션 등 애플리케이션에 악의적인 데이터를 삽입하는 공격입니다.
2. **Broken Authentication**: 적절하지 않은 구현으로 인해 공격자가 사용자의 신원을 도용할 수 있게 하는 취약점입니다.
3. **Sensitive Data Exposure**: 중요한 데이터가 암호화되지 않고 노출되어, 공격자가 접근할 수 있는 상태를 의미합니다.
4. **XML External Entities (XXE)**: 공격자가 외부 엔티티를 이용하여 원격 콘텐츠에 접근하거나 서버에 있는 다른 파일을 읽을 수 있게 하는 취약점입니다.
5. **Broken Access Control**: 제한되어야 할 자원에 대한 무단 접근을 허용하는 취약점입니다.
6. **Security Misconfiguration**: 보안 설정이 적절하지 않게 구성되어 있어 발생하는 취약점입니다.
7. **Cross-Site Scripting (XSS)**: 애플리케이션에서 사용자에게 제공하는 데이터가 적절한 검증 없이 실행되는 경우, 공격자가 사용자 측 스크립트를 삽입할 수 있게 하는 취약점입니다.
8. **Insecure Deserialization**: 악의적으로 조작된 객체가 역직렬화 과정에서 실행되어 애플리케이션에 악영향을 미치는 취약점입니다.
9. **Using Components with Known Vulnerabilities**: 알려진 취약점이 있는 컴포넌트를 사용함으로써 발생하는 보안 문제입니다.
10. **Insufficient Logging & Monitoring**: 충분하지 않은 로깅과 모니터링으로 인해 보안 위반 사항이 제때에 탐지되지 않고 대응이 늦어지는 문제입니다.

