# 목표
- CD 구성
- EKS (Auto Mode) 환경의 WEB/WAS 애플리케이션을 ArgoCD로 배포하는 `GitOps CD 저장소`
    -
    - 제품 : Argo CD, Flux, ...
    - 특정 내용이 변경되면(계속 감시) 이를 트리거로 하여 배포 함 (CI와 느슨한 연결)
        - kustomization.yaml : Argo CD 계속 모니터링, CI에서 결과로 수정(CI에서)
- 간단 workflow
    ```
        GitHub Actions Push -> ... -> kustomization.yaml 수정 -> Argo Cd 감지 -> ... -> Pod 순차 교체
        (배포) -> 서비스 업데이트 (무중단 실시간 반영)
    ```