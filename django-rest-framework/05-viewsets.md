## ViewSets
`ViewSet` 클래스는 단순히 **클래스 기반 뷰** 유형이며, method 핸들러를 제공하지 않는다. 대신 `list()` 및 `create()`와 같은 액션을 제공합니다.

`ViewSet`의 method 핸들러는 `.as_view()` 메서드를 사용하여 해당 액션에만 바인딩됩니다.

일반적으로 urlconf의 뷰셋에 뷰를 명시 적으로 등록하는 대신, viewset를 `router` 클래스로 등록하면 자동으로 urlconf가 결정됩니다.

일반 view 및 URL 구성을 사용하면보다 명확하게 제어 할 수 있습니다. `ViewSets`는 신속하게 시작하고 실행하려는 경우, 또는 대규모 API가 있고 전체적으로 일관된 URL 구성을 적용하려는 경우 유용합니다.

## API Reference

### GenericViewSet
`GenericViewSet` 클래스는 `GenericAPIView`에서 상속

`GenericViewSet` 클래스를 사용하려면 클래스를 재정의하고 필요한 mixin 클래스를 혼합하거나 액션 구현을 명시 적으로 정의하십시오.

### ModelViewSet
`ModelViewSet` 클래스는 `GenericAPIView`를 상속

`ModelViewSet` 클래스에서 제공하는 작업은 `.list()`, `.retrieve()`, `.create()`, `.update()`, `.partial_update()` 및 `.destroy()`입니다.

### ReadOnlyModelViewSet
`ReadOnlyModelViewSet` 클래스는 또한 `GenericAPIView`에서 상속

`ModelViewSet`과 달리 '읽기 전용'액션 인 `.list()` 및 `.retrieve()` 만 제공됩니다.
