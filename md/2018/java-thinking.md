---
title: Java Thinking
date: 2018-01-21
tags: java
---

泛型
```
List<Object> list = new ArrayList<String>(); //wrong!

public static GroupScoreDTO parseResp(SearchResponse resp) {
     GroupScoreDTO ret = new GroupScoreDTO();
     ret.setSomeField(aaa);
     ...
     return ret;
}

->

public static <T extends OrgBaseDTO> T parseResp(**, Class<T> clz) {
    T ret = clz.newInstance();
    ret.someBaseDTOMethod(aaa);
    ...
    return ret;
}

GroupScoreDTO dto = parseResp(***, GroupScoreDTO.class); //dto.setSpecificMethod(**)
FamilyScoreDTO dto = parseResp(***, FamilyScoreDTO.class); //...
CollegeScoreDTO dto = parseResp(***, CollegeScoreDTO.class); //...
```
反射

回调函数：


inner class：
```
    //This class is an inner class, but does not use its embedded reference to the object which created it.  
    //**This reference makes the instances of the class larger, and may keep the reference to the creator object alive longer than necessary.  
    //If possible, the class should be made static.

	private static class RobotSortComparator implements Comparator<Map<String, String>> {
		String sortMetric;
		RobotSortComparator(String sortMetric) {
			this.sortMetric = sortMetric;
		}

		@Override
		public int compare(Map<String, String> o1, Map<String, String> o2) {
			if(o1.get(sortMetric) == null || o2.get(sortMetric) == null ) {
				return 0;
			}
			Double d1 = Double.valueOf(o1.get(sortMetric));
			Double d2 = Double.valueOf(o2.get(sortMetric));
			if (d1.compareTo(d2) < 0) {
				return -1;
			} else if (d1.compareTo(d2) > 0) {
				return 1;
			} else {
				return 0;
			}
		}
	}
```

```
map.entrySet() -> map.keySet()

This method accesses the value of a Map entry, using a key that was retrieved from a keySet iterator. 
It is more efficient to use an iterator on the entrySet of the map, to avoid the Map.get(key) lookup.
```

