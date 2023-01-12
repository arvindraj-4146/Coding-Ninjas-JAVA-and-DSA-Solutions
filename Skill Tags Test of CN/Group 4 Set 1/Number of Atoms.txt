import java.util.ArrayList;
import java.util.Collections;
import java.util.Comparator;
import java.util.HashMap;
import java.lang.Character.*;

class Number {
	int value;

	Number(int value) {
		this.value = value;
	}
}

class Pair {

	String monoAtom;
	int count;

	Pair(String monoAtom, int count) {
		this.monoAtom = monoAtom;
		this.count = count;
	}
}

class SortComparator implements Comparator<Pair> {

	@Override
	public int compare(Pair p1, Pair p2) {

		return p1.monoAtom.compareTo(p2.monoAtom);
	}

}

public class Solution {

	public static void countNumberOfAtomsHelper(String formula, Number index, HashMap<String, Integer> cntOfAtoms,
			int n) {
		// String to store the element name.
		String element = "";

		// Variable to store the count of the current atom.
		int atomCount = 0;

		while (index.value < n && (formula.charAt(index.value) != ')')) {
			element = "";
			atomCount = 0;

			// Check for any uppercase alphabets for the element name.
			if (Character.isLetter(formula.charAt(index.value)) && Character.isUpperCase(formula.charAt(index.value))) {

				element += formula.charAt(index.value++);
			}

			// Then check if the uppercase alphabets are followed by any lowercase alphabets.
			while (index.value < n && Character.isLetter(formula.charAt(index.value))
					&& Character.isLowerCase(formula.charAt(index.value))) {

				element += formula.charAt(index.value++);
			}

			// Check for digits following the alphabets and update the atomCount value.
			while (index.value < n && Character.isDigit(formula.charAt(index.value))) {
				atomCount = atomCount * 10 + (formula.charAt(index.value++) - '0');
			}

			// If there were no digits then we want to set a value of '1' for the atomCount.
			if (atomCount == 0) {
				atomCount = 1;
			}

			// Update the count of the element in the map.
			if (element != "") {
				if (cntOfAtoms.containsKey(element))
					cntOfAtoms.put(element, cntOfAtoms.get(element) + atomCount);
				else
					cntOfAtoms.put(element, atomCount);

			}

			// If we encounter '(' then we need to recursively process the string within ().
			if (index.value < n && formula.charAt(index.value) == '(') {
				index.value++;

				// Create a new inner map which will be populated in the recursion call.
				HashMap<String, Integer> innerFormulaCnt = new HashMap<>();

				// Recursively call the function again with the new Inner map.
				countNumberOfAtomsHelper(formula, index, innerFormulaCnt, n);

				// After the recursive call, update the main map with the contents of the inner.
				// Map.
				for (String key : innerFormulaCnt.keySet()) {
					if (cntOfAtoms.containsKey(key))
						cntOfAtoms.put(key, cntOfAtoms.get(key) + innerFormulaCnt.get(key));
					else
						cntOfAtoms.put(key, innerFormulaCnt.get(key));
				}
			}
		}

		// If we encountered ')', then we have to check if a digit follows it.
		// If a digits exists after ')' then we have to multiple all our atom's value with the digit.
		if (index.value < n && formula.charAt(index.value) == ')') {

			index.value++;
			atomCount = 0;

			// Build the complete digit value.
			while (index.value < n && Character.isDigit(formula.charAt(index.value))) {
				atomCount = atomCount * 10 + (formula.charAt(index.value++) - '0');
			}

			// If there were no digits then we want to set a value of '1' for the atomCount.
			if (atomCount == 0) {
				atomCount = 1;
			}

			for (String key : cntOfAtoms.keySet()) {
				cntOfAtoms.put(key, cntOfAtoms.get(key) * atomCount);
			}
		}
	}

	public static String countNumberOfAtoms(String formula) {

		// Map to store the count of atoms.
		HashMap<String, Integer> cntOfAtoms = new HashMap<>();

		Number index = new Number(0);
		countNumberOfAtomsHelper(formula, index, cntOfAtoms, formula.length());

		// Vector that will store the final count of atoms.
		ArrayList<Pair> atomCnt = new ArrayList<>();

		for (String key : cntOfAtoms.keySet()) {
			Pair p = new Pair(key, cntOfAtoms.get(key));
			atomCnt.add(p);
		}

		// Sort the vector to get elements in lexicographical order.
		Collections.sort(atomCnt, new SortComparator());

		// String to store the final result.
		String ans = "";

		// Build a string from the vector.
		for (Pair atom : atomCnt) {

			String monoAtom = atom.monoAtom;
			int cnt = atom.count;

			ans += monoAtom;

			if (cnt > 1) {
				ans += cnt;
			}
		}

		return ans;
	}
}