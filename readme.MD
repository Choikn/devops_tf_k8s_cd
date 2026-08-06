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

# 전체 workflow
  ```text
  개발자 Push
  ↓
  tf-k8s-ci / GitHub Actions
  ├─ Terraform·Docker 검증
  ├─ WEB/WAS 이미지 Build
  ├─ ECR Push
  └─ devops_tf_k8s_cd의 이미지 태그를 Git Commit SHA로 변경
            ↓
      devops_tf_k8s_cd main
            ↓
      Argo CD 자동 감지
            ↓
      EKS Auto Mode 동기화
            ↓
  Public ALB → WEB Service → WEB Pod → WAS Service → WAS Pod → RDS
  ```

  - Argo CD는 `k8s/overlays/dev` 여기 감시 -> kustomization.yaml 존재
  - CI는 ECR에 업로드된 이미지의 태그(Git Commit SHA는 push 할때마다 변경)를 kustomization.yaml 기록
  - 변경되면 CD 진행됨