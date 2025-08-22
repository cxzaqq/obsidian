무언가를 검색할 때 자동 완성 기능은 어떻게 동작하는 거고 어떤 자료 구조를 사용할까?

# 트라이(17.1)

원래 발음은 "트리"가 맞지만 보편적으로 쓰이는 트리가 이미 있기에 "트라이"로 발음한다.

트라이 노드는 자식 노드 개수에 제한이 없다.
각 트라이 노드마다 해시 테이블을 포함한다.

예시를 보자

![[트라이.png]]

`*` 표시는 단어의 끝을 나타낸다. 위 예시에서 "batter"의 첫 번째 "t"는 키가 둘인 노드를 가리킨다. 하나는 `*`이고 하나는 `t`다. 즉 "bat" 자체가 하나의 단어임과 동시에 더 긴  "batter"의 접두사임을 나타낸다.

## 트라이 검색

검색의 목적은 크게 두 가지다.
문자열이 완전한 단어인지 알아내는 것.
문자열이 최소한 어떤 단어의 접두사인지 알아내는 것.

접두사 검색 알고리즘은 다음과 같은 단계를 수행한다.
1. currentNode에 루트 노드 할당.
2. 검색 문자열의 각 문자를 순회
3. 검색 문자열의 각 문자를 가리키며 currentNode에 그 문자를 키로 하는 자식이 있는지 본다.
4. 자식이 없으면 None 반환
5. 자식이 있으면 currentNode를 그 자식 노드로 업데이트. 이후 반복
6. 끝까지 순회했다면 검색 문자열을 찾은 것

트라이는 검색 시 각 노드의 해시 테이블을 사용해 한 단계만에 적절한 자식 노드를 찾는다. 따라서 검색은 검색하려는 문자열의 문자 수만큼의 단계면 충분하다.
하지만 이를 O(N)이라 표현하면 트라이 내 노드 수를 뜻하는 것으로 오해할 수 있다.

따라서 트라이의 복잡도는 보통 O(K)라 부른다. K는 문자 수이다.
트라이에 데이터가 얼마나 있든 만약 세 문자로 이뤄진 단어라면 항상 3 단계만 걸리는 것이다.

## 트라이 삽입

삽입은 검색과 비슷하다.

1. currentNode에 루트 노드 할당
2. 문자열의 각 문자를 순회
3. currentNode에 그 문자를 키로 하는 자식이 있는지 확인
4. 자식이 있으면 currentNode를 그 자식 노드로 업데이트. 이후 반복
5. 없으면 자식 노드 생성 후 currentNode 업데이트
6. 마지막 문자까지 끝났으면 마지막 노드에 `*` 추가

## 자동 완성 개발(17.6)

자동 완성 사용 시 키를 `*` 이 문자로 하면 값은 따로 없었다 하지만 이 값을 사용 빈도 같은 데이터를 추가하면 더욱 유의미한 자동 완성을 개발할 수 있다.

- 자바는 트라이가 따로 구현되어 있지 않아 직접 구현하거나 라이브러리를 사용할 수 있다. 구현은 조금씩 다를 수 있으나 개념은 비슷하다.

```java
import java.util.*;

class TrieNode {
	Map<Character, TrieNode> children = new HashMap<>();
	int frequency = 0; // 단어 끝에서만 유효
	boolean isEndOfWord = false;
}

// 단어와 빈도 저장용 클래스
private static class WordEntry {
	String word;
	int frequency;
	WordEntry(String word, int frequency) {
		this.word = word;
		this.frequency = frequnecy;
	}
}

public class Trie {
	private final TrieNode root = new TrieNode();
	
	// 단어 삽입
	public void insert(String word) {
		TrieNode node = root;
		
		for (char c : word.toCharArray()) {
			/*
			V computeIfAbsent(K key, Function<? super K, ? extends V> mappingFunction)
			key: 찾으려는 키
			mappingFunction: 값이 없을 때 새로 생성할 방법
			
			key가 이미 있으면 기존 값을 리턴
			없으면 mappingFunction 실행해서 새 값을 넣고 리턴
			*/
			node = node.children.computeIfAbsent(c, k -> new TrieNode());
		}
		node.isEndOfWord = true;
	}
	
	// prefix 기반 자동 완성 (상위 N개)
	public List<String> autoComplete(String prefix, int limit) {
		TrieNode node = root;
		
		for (char c : prefix.toCharArray()) {
			node = node.children.get(c);
			if (node == null) return Coolections.emptyList();
		}
		
		// 후보 단어 수집
		List<WordEntry> candidates = new ArrayList<>();
		dfs(node, new StringBuilder(prefix), candidates);
		
		// 빈도수 내림차순 정렬 후 상위 N 개
		candidates.sort((a, b) -> b.frequency - a.frequency);
		
		List<String> results = new ArrayList<>();
		for (int i = 0; i < Math.min(limit, candidates.size()); i++) {
			results.add(candidates.get(i).word)
		}
		return results;
	}
	
	// dfs
	private void dfs(TrieNode node, StringBuilder current, List<WordEntry> results) {
		if (node.isEndOfWord) {
			results.add(new WordEntry(current.toString(), node.frequency));
		}
		for (Map.Entry<Character, TrieNode> entry : node.children.entrySet()) {
			current.append(entry.getKey());
			dfs(entry.getValue(), current, results);
			current.deleteCharAt(current.length() - 1);
		}
	}
}
```