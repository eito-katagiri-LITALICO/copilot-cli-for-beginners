---
name: pytest-gen
description: 包括的な pytest テストを生成します - テストの生成、テストスイートの作成、または Python コードのテスト時に使用します
---

# Pytest Generation Skill

テストを生成する際は、以下の構成に従ってください。

## テストの構成

- テスト対象の関数ごとにテストをグループ化する
- 複数の入力値には `@pytest.mark.parametrize` を使用する
- 共通のセットアップには fixture を使用する
- arrange/act/assert パターンに従う

## カバレッジ要件

- ハッピーパス（期待される通常の使用方法）
- エッジケース（空文字列、None、境界値）
- エラーケース（無効な入力、ファイルが見つからない、型の誤り）
- 統合テスト（複数の関数が連携して動作すること）

## テンプレート

```python
import pytest
from module_under_test import function_to_test


@pytest.fixture
def sample_data():
    """Provide shared test data."""
    return {"key": "value"}


class TestFunctionName:
    """Tests for function_name."""

    def test_happy_path(self, sample_data):
        result = function_to_test(valid_input)
        assert result == expected_output

    def test_empty_input(self):
        result = function_to_test("")
        assert result == expected_for_empty

    @pytest.mark.parametrize("input_val,expected", [
        ("valid", True),
        ("", False),
        (None, False),
    ])
    def test_various_inputs(self, input_val, expected):
        assert function_to_test(input_val) == expected
```
