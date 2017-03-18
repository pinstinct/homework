## Serializer relations
관계 필드는 모델 관계를 나타내는 데 사용됩니다. `ForeignKey`, `ManyToManyField` 및 `OneToOneField` 관계 및 사용자 정의 관계(예: `GenericForeignKey`)를 적용 할 수 있습니다.


## API Reference
- StringRelatedField
  - read only
  - `many` : 일대다 관계에 적용하려면, `True` 설정
- PrimaryKeyRelatedField
  - 기본 키(primary key)를 사용하여 관계의 대상을 나타내는 데 사용, read-write (default)
  - `read_only`
  - `allow_null`
  - `many`
- HyperlinkedRelatedField
  - 하이퍼 링크를 사용하여 관계의 대상을 나타내는 데 사용, read-write (default)
  - `view_name` (required)
  - `read_only`
  - `lookup_field`
  - `lookup_url_kwarg`
  - `format`


## Nested relationships
기본적으로 중첩 serializer는 읽기 전용입니다.
```python
class TrackSerializer(serializers.ModelSerializer):
    class Meta:
        model = Track
        fields = ('order', 'title', 'duration')

class AlbumSerializer(serializers.ModelSerializer):
    tracks = TrackSerializer(many=True, read_only=True)

    class Meta:
        model = Album
        fields = ('album_name', 'artist', 'tracks')
```

## Further notes
### Reverse relations
역방향 관계는 `ModelSerializer` 및 `HyperlinkedModelSerializer` 클래스에 자동으로 포함되지 않습니다. 역방향 관계를 포함 시키려면 필드 목록에 명시적으로 추가해야합니다.

```python
class Track(models.Model):
    album = models.ForeignKey(Album, related_name='tracks', on_delete=models.CASCADE)
```
역방향 관계에 대한 관련 이름을 설정하지 않은 경우 fields 인수에 자동으로 생성 된 관련 이름을 사용해야합니다.

```python
class AlbumSerializer(serializers.ModelSerializer):
    class Meta:
        fields = ('track_set', ...)
```
